---
layout: page
title: 分布式持久内存文件系统的研究准备
subtitle: 雄关漫道真如铁，而今迈步从头越
use-site-title: true
---

## DDST持久内存文件系统工作概览

|项目名|论文Title|发表期刊/会议|工作简介|
|:-:|:-:|:-:|:-:|
|HMFS|HMFS: A Hybrid Memory File System with Version Consistency|JPDC|一种新型混合平行架构（NVM+DRAM）的内存文件系统，使用mmap来创建Load/Store接口，利用版本来维护文件一致性|
|/|A Consistency Mechanism for NVM-Based in-Memory File Systems|CF|一种新型的维护基于NVM的文件系统的一致性方案，使用惰性确认的快照策略来获得高一致性保障|
|HMVFS|HMVFS: A Hybrid Memory Versioning File System|MSST|一种基于HMFS开发的支持多版本快速回退的混合架构（NVM+DRAM）内存文件系统|
|WARP|Adaptive Prefetching for Accelerating Read and Write in NVM-based File Systems|ICCD|感知文件系统中的一种自适应预取算法模块，主要为了解决由于NVM读写性能不对称时文件读写负载不均衡所导致的性能下降问题|
|Versioning-mmap|Versioning-mmap: A DAX-enabled Mmap Mechanism for Versioning In-memory File Systems|IPCCC|一种新型的mmap机制，主要用来解决基于DAX的内存文件系统维护一致性（COW方式）时的严重开销问题|

### 黄老师建议：

将一个作为工作基础，然后铺陈开来，讲述工作创新点。这是要汇报的第一部分。

接下来，结合近年来的分布式内存文件系统的研究工作，总结规律和特色，前瞻我们自己可以做的工作，列出背景与动机。这是要汇报的第二部分。


## 基于NVM的内存文件系统相关工作（旧）

### BPFS

[点击进入项目文档](https://github.com/xpsair/bpfs)

**摘要**

Modern computer systems have been built around the assumption that persistent storage is accessed via a slow, block-based interface. However, new byte-addressable, persistent memory technologies such as phase change memory (PCM) offer fast, fine-grained access to persistent storage.

In this paper, we present a file system and a hardware architecture that are designed around the properties of persistent, byteaddressable memory. Our file system, BPFS, uses a new technique called short-circuit shadow paging to provide atomic, fine-grained updates to persistent storage. As a result, BPFS provides strong reliability guarantees and offers better performance than traditional file systems, even when both are run on top of byte-addressable, persistent memory. Our hardware architecture enforces atomicity and ordering guarantees required by BPFS while still providing the performance benefits of the L1 and L2 caches.

Since these memory technologies are not yet widely available, we evaluate BPFS on DRAM against NTFS on both a RAM disk and a traditional disk. Then, we use microarchitectural simulations to estimate the performance of BPFS on PCM. Despite providing strong safety and consistency guarantees, BPFS on DRAM is typically twice as fast as NTFS on a RAM disk and 4-10 times faster than NTFS on disk. We also show that BPFS on PCM should be significantly faster than a traditional disk-based file system.


### PMFS

[点击进入项目文档](https://github.com/linux-pmfs/pmfs)

**摘要**

PMFS is a file system for persistent memory. The file system is optimized to be lightweight and efficient in providing access to persistent memory that is directly accessible via CPU load/store instructions. It manages the persistent memory directly and avoids the block driver layer and page cache layer and thus provides synchronous reads and writes to persistent area. It supports all the existing POSIX style file system APIs so that the applications need not be modified to use this file system. In addition, PMFS provides support for huge pages to minimize TLB entry usage and speed up virtual address lookup. PMFS's mmap interface can map a file's data directly into the process's address space without any intermediate buffering. This file system has been validated using DRAM to emulate persistent memory. Hence, PMFS also provides an option to load the file system from a disk-based file into memory during mount and save the file system from memory into the disk-based file during unmount. PMFS also guarantees consistent and durable updates to the file system meta-data against arbitrary system and power failures. PMFS uses journaling (undo log) to provide consistent updates to meta-data.


### SCMFS

[点击进入论文页面](https://dl.acm.org/citation.cfm?id=2063436)

**摘要**

This paper considers the problem of how to implement a file system on Storage Class Memory (SCM), that is directly connected to the memory bus, byte addressable and is also non-volatile. In this paper, we propose a new file system, called SCMFS, which is implemented on the virtual address space. In SCMFS, we utilize the existing memory management module in the operating system to do the block management and keep the space always contiguous for each file. The simplicity of SCMFS not only makes it easy to implement, but also improves the performance. We have implemented a prototype in Linux and evaluated its performance through multiple benchmarks.



### NOVA

[点击进入项目文档](https://github.com/NVSL/linux-nova)

NOVA's goal is to provide a high-performance, full-featured, production-ready file system tailored for byte-addressable non-volatile memories (e.g., NVDIMMs and Intel's soon-to-be-released 3DXpoint DIMMs). It combines design elements from many other file systems to provide a combination of high-performance, strong consistency guarantees, and comprehensive data protection. NOVA support DAX-style mmap and making DAX performs well is a first-order priority in NOVA's design. NOVA was developed by the Non-Volatile Systems Laboratory in the Computer Science and Engineering Department at the University of California, San Diego.

NOVA is primarily a log-structured file system, but rather than maintain a single global log for the entire file system, it maintains separate logs for each file (inode). NOVA breaks the logs into 4KB pages, they need not be contiguous in memory. The logs only contain metadata.

File data pages reside outside the log, and log entries for write operations point to data pages they modify. File modification uses copy-on-write (COW) to provide atomic file updates.

For file operations that involve multiple inodes, NOVA use small, fixed-sized redo logs to atomically append log entries to the logs of the inodes involned.

This structure keeps logs small and make garbage collection very fast. It also enables enormous parallelism during recovery from an unclean unmount, since threads can scan logs in parallel.

NOVA replicates and checksums all metadata structures and protects file data with RAID-4-style parity. It supports checkpoints to facilitate backups.


## 分布式持久内存文件系统的研究动机

我们为什么希望设计一种**分布式**的内存文件系统呢？

- 分布式系统的优点是通过并行计算，大大提高系统的整体性能。而RDMA的远程内存直访省去了分布式环境下CPU指令执行的时间开销，可进一步提升系统的并行处理能力。

现在的持久内存文件系统有什么问题？

- 缺少并行计算，单节点的处理性能终归有限。

NVM+RDMA对于持久内存文件系统有什么样潜在的巨大优势？

- NVM的优势是高读写性能，可字节寻址，非易失性质；
- RDMA的优势是分布式/并行计算，节省CPU开销，大大提高处理性能。


## 分布式内存文件系统的相关工作

- Octopus: an RDMA-enabled Distributed Persistent Memory File System （Tsinghua USENIX ATC 17）
- Ceph over Accelio. "https://www.cohortfs.com/ceph-over-accelio", 2014
- GlusterFS on RDMA. "https://gluster.readthedocs.io/en/latest/AdministratorGuide/RDMATransport/"
- ISLAM, N. S., WASI-UR RAHMAN, M., LU, X., AND PANDA,D. K. High performance design for hdfs with byte-addressability
of nvm and rdma. In Proceedings of the 2016 International Conference on Supercomputing (2016)
- Crail: A Fast Multi-tiered Distributed Direct Access File System. https://github.com/zrlio/crail, 2017
- STUEDI, P., TRIVEDI, A., METZLER, B., AND PFEFFERLE, J. DaRPC: Data center rpc. In Proceedings of the ACM Symposium
on Cloud Computing (SoCC) (2014)



## 总结与规划

以清华大学发表的Octopus为基础，追本溯源，首先泛读当前已有的 RDMA+持久内存文件系统 工作，理解各自的系统特点，优势及局限性。然后精读Octopus论文，有必要的话阅读源码并再实现一次。接下来根据实验观察或者论文阅读积累找到自己的研究动机和选题点，开始写DDST的分布式持久内存文件系统。



<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->
