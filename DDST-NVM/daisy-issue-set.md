---
layout: page
title: 情系 863
subtitle: 内存管理系统大数据应用之BUG不断
use-site-title: true
---

## 总体描述

这是博文在使用轨迹数据库调用持久内存系统Daisy的接口时出现的一系列问题的总结：

1. p_free 释放内存后再进行 p_malloc，分配的地址访问错误（Segmentation fault）。这也是最初出现在版本 3.12.49 上的问题。
2. p_malloc 初次分配地址，该地址就无法访问。
3. p_new 在系统初次启动后就返回某 ID 已经存在，而我们现在的所用的内存并不具备非易失性。
2. p_malloc 分配不出（p_init预开辟的）有效地址，返回NULL。


一个简单的 m-test 程序代码如下：

```
#include<stdio.h>
#include "p_mmap.h"

int main() {
    int ret = p_init(64*1024); // Initiate a 64KB native heap
    if (ret < 0) {
        printf("Initiation failed!\n");
        return -1;
    }

    int *intAddr = (int *)p_malloc(sizeof(int)); // malloc a PM address for INTEGER data

    if (intAddr == NULL) {
        printf("Malloc failed!\n");
        return -1;
    }

    *intAddr = 1;
    printf("Malloc address is %p and its value is %d.\n", intAddr, *intAddr);

    p_bind(1, intAddr, sizeof(int)); // bind intAddr data to native heap with a native ID 1.

    return 0;
}
```

该段函数在我的本机上运行会malloc失败，说明malloc函数确实出了问题，不过离奇的是在NVDIMM上这一段代码可以完美运行。

然而，让我比较安慰的一点是：p_init 函数是成功的，这说明预分配过程没有问题。即便是要修改，也不必在内核态进行调整，只需要维护好用户态本地堆空间的分配即可。

### p_malloc 原理及深入分析

p_malloc 函数原来的实现代码为：

```
void* p_malloc(int size) {
    if (size < 0)
        printf("error: p_malloc, size must be greater than 0");
        return NULL;
    }

    if (pBaseAddr == NULL) {
        printf("error: call p_init first\n");
        return NULL;
    }

    // save size info
    size += 4;

    char curChar;
    unsigned char mask;
    
    enum {
        STOP = 0,
        LOOKING
    } state;

    state = STOP;
    int iStartBit = 0;
    int *ptrSize = NULL;

    int n, i;
    int ag = 1;
    for (i=0; i<iBitsCount; i++) {
        n = next_bit + i;
        if (n >= iBitsCount && ag) {
            state = STOP;
            next_bit = 0;
            n = -1;
            ag = 0;
            continue;
        }

        mask = 1 << (7 - n%8);
        if (!(pBaseAddr[n/8] & mask)) {
            // nth bit is empty

            switch (state) {
                case STOP:
                    iStartBit = n;
                    state = LOOKING;
                case LOOKING:
                    if ((n - iStartBit + 1) * BITMAPGRAN >= size) {
                        // we find it 
                        // printf("we find it, ready to set bit\n"); 
                        set_bit_to_one(iStartBit, n);
                        ptrSize = (int *)(pBaseAddr + SHM_SIZE/(1 + BITMAPGRAN*8) + iStartBit * BITMAPGRAN);
                        *ptrSize = size;
			next_bit = n + 1;

                        return (void *)(pBaseAddr + SHM_SIZE/(1 + BITMAPGRAN*8) + iStartBit * BITMAPGRAN + 4); 
                    }

                    break;
                default:
                    break;
            }
        } else {
            // nth bit is not empty
            switch (state) {
                case LOOKING:
                    state = STOP;
                    break;
                default:
                    break;
            }
        }
    }

    return NULL;
}


```

我个人觉得这份 p_malloc 实现既不简洁也不优雅，因为直到现在我都对它是如何利用 bitmap 管理堆空间的感到迷惑，而且宏定义 BITMAPGRAN 与全局静态变量 iStartBits 的使用也增加了代码复杂性和出错概率。

现在最大的问题是 p_malloc 甚至不能分配出一个范围在本地堆内的有效地址。所以，我决定重写 p_malloc 函数，具体需要考虑的几个问题是：
- 第一，如何管理整个预分配的（使用和未使用的）本地堆空间？
- 第二，如何取回之前保存的数据？
- 第三，如何解决外部碎片？

首先对于整个本地堆空间，容易想到的是将它分为两大部分：使用空间（p_malloc 分配出去的空间）和空闲空间（未分配的空间）。那么自然地，就可以利用 FLAG 元数据来标记某一块空间是使用还是空闲，我设计的元数据管理结构如下：

|:-:|:-:|
|MetaData Structure| SIZE |
| FLAG & ID | 4 byte |
| Size | 4 byte |
| nextLoc | 4 byte |


对于空间分配 p_malloc 的算法，代码实现如下：

```
/* 2018-08-07: a new p_malloc implementation without bitmap but pid as input parameter */
void *malloc(int pid, int size) {
    if (!pBaseAddr) {
        printf("Error: call p_init first!\n");
        return NULL;
    }

    int nextLoc = 1;
    void *pLeapAddr = pBaseAddr;
    int length;
    int toAllocateSize = size + 3 * sizeof(int);

    /* First fit for allocation in user heap */
    while (nextLoc != 0) {
        nid = *(int *)pLeapAddr; // nid = 0 means free
        length = *((int *)pLeapAddr + 1); // length is the size corresponding to p_malloc
        nextLoc = *((int *)pLeapAddr + 2); // the offset of next starting address for free/used chunk 
        // nosense = *((int *)pLeapAddr + 3); // need it or not?

        /* find a free chunk with enough space */
        if (nid == 0 && length > = toAllocateSize) {
            // 
            printf("Finad a free chunk with enough space %d.\n", length);
            /* set important metada for current and next chunk */
            /* For current chunk */
            *(int *)pLeapAddr = pid;
            *((int *)pLeapAddr + 1) = size;
            *((int *)pLeapAddr + 2) = toAllocateSize;
            /* For next chunk */
            void *pNextAddr = pLeapAddr + toAllocateSize;
            *(int *)pNextAddr = 0; // free chunk
            *((int *)pNextAddr + 1) = length - toAllocateSize; // 
            *((int *)pNextAddr + 2) = 0; // stop flag for search


            /* return the used address for users */
            return (pLeapAddr + 3 * sizeof(int));
        }

        /* if in this round a free chunk not found or size not satisfied, continue */
        pLeapAddr += 3 * sizeof(int) + length;
    }

    /* nextLoc == 0 but no free chunk satisifies the applied space */
    printf("No free memory, please p_init a larger space or clear the heap memory instead.\n");
    return NULL;
}


```

更新 p_free 的代码如下所示：

```
/* p_free is corresponding to p_malloc */
int p_free(int pid) {
    if (!pBaseAddr) {
        printf("Error: call p_init first!\n");
        return -1;
    }
    void *pLeapAddr = pBaseAddr;

    int nid, length, nosense;
    int nextLoc = 1;
    while (nextLoc != 0) {
        nid = *(int *)pLeapAddr; // nid = 0 means free
        length = *((int *)pLeapAddr + 1); // length is the size corresponding to p_malloc
        nextLoc = *((int *)pLeapAddr + 2); // the offset of next starting address for free/used chunk 
        // nosense = *((int *)pLeapAddr + 3); // need it or not?

        /* reach the end of the allocated memory region and pid not found */


        if (nid == pid) {
            printf("Find id %d in the heap.\n", pid);

            memset(pLeapAddr + 4*sizeof(int), 0, length);
            *(int *)pLeapAddr = 0; // pid = 0, free chunk

            // TODO: recycle contigous free memory in PM
            // continue; // Delete all data with the same pid
            return 0;
        }

        pLeapAddr += (length + 3 * sizeof(int));
    }

    if (nextLoc == 0) {
        printf("Cannot find pid %d in native heap.\n", pid);
        return -1;
    }

    printf("Unknown type of error occured!\n");
    return -1;
}

```



更新 p_init 代码如下所示：

```

int p_init(int size) {
    int iRet = 0;

    if (pBaseAddr != NULL) {
        return -1;
    }
    if (size < 0) {
        return -1;
    }

    // SHM_SIZE = size;

    iRet = p_get_small_region(hpid, size);
    if (iRet < 0) {
        printf("Error:p_get_small_region call failed!\n");
        return -1;
    }

    pBaseAddr = p_mmap(NULL, size, PROT_READ | PROT_WRITE, HPID);
    if (!pBaseAddr) {
        printf("p_mmap return NULL!\n"); // No sense
        return -1;
    }

    /* If this pre-allocated native heap is not in use, clear it with setting all the memory bytes to be 0s */
    int *startLoc = (int *)pBaseAddr;
    pBaseAddr += 4;
    if (*startLoc != 4 || hpid >= 10000) {
        *startLoc = 4;
        p_clear(size);
    }

    // printf("pBaseAddr = %p.\n", pBaseAddr);
}

```
更新 p_clear 代码如下所示：

```
int p_clear() {
    if (!pBaseAddr) {
        printf("Error: call p_init() first!\n");
        return -1;
    }
    // set all the bytes to be 0s in this heap
    memset(pBaseAddr+4, 0, size-4);
    /* Set the first contiguous free chunk */
    *((int *)pBaseAddr + 1) = size - 4 * sizeof(int); // length = size - (sizeof(startLoc) + sizeof(nid) + sizeof(length) + sizeof(nextLoc)
    *((int *)pBaseAddr + 2) = 0; // a flag for stop searching
    return 0;
} 
```

增加 p_get_malloc 代码如下所示：

```

void *p_get_malloc(int pid) {
    if (!pBaseAddr) {
        printf("Error: call p_init first!\n");
        return -1;
    }
    void *pLeapAddr = pBaseAddr;

    int nid, length;
    int nextLoc = 1;
    while (nextLoc != 0) {
        nid = *(int *)pLeapAddr; // nid = 0 means free
        length = *((int *)pLeapAddr + 1); // length is the size corresponding to p_malloc
        nextLoc = *((int *)pLeapAddr + 2); // the offset of next starting address for free/used chunk 

        if (nid == pid) {
            printf("Find id %d in the heap.\n", pid);
            return (pLeapAddr + 3 * sizeof(int));
        }

        /* If corresponding pid not found in this round, continue */
        pLeapAddr += (length + 3 * sizeof(int));
    }

    if (nextLoc == 0) {
        printf("Cannot find pid %d in native heap.\n", pid);
        return NULL;
    }

    printf("Unknown type of error occured!\n");
    return NULL;

}

```



<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->