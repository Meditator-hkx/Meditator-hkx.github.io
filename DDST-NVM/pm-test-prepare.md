---
layout: page
title: 持久内存管理的持久化测试准备
subtitle: Deadline 迫在眉睫，一切均需部署完毕
use-site-title: true
---

## 测试文档

一份测试报告，测试报告的模板可参考上次测试的报告。

## 测试任务

1. 持久化功能测试
 - 注意，持久化功能的含义是：任何写入NVM空间中的数据应当在程序退出或者系统崩溃后寻回。如果取回的数据与写入时相同，则说明持久化功能正确完成。

2. 持久化性能测试
 - 所谓性能测试，即是对比Store数据直接到NVM的方式比传统的持久化方法（从内存持久化到磁盘的操作通常是fysnc或者msync），根据项目结题指标，性能提升应在5% 以上：
 

## 基本简介

NVM是一种新型非易失存储器件，它兼具传统的DRAM和DISK二者的优势，可按字节读取数据，非易失，容量大。正因为它在持久化数据方面无与伦比的优势，我们考虑将其用作存储持久数据的空间。我们做了一套管理非易失内存的支持系统，它提供给用户一些使用NVM空间的接口，并且在操作系统层面上维护用户所使用的NVM空间。通过简单地Load/Store指令，用户程序就可以加载和存储需要持久的数据（或数据结构）。另外，由于DRAM本身的易失性，存储在其中的数据在程序故障或系统崩溃以后会全部丢失，但NVM中的数据却会一直存在。这就可能导致数据一致性方面的问题，因此除了持久内存的分配接口，我们也提供给用户一致性保障的接口，来保障系统持久化的正确性。比起传统的内存管理系统，我们设计的持久内存系统在持久化方面有更高的性能。下面我们将设计实验来证明我们的Daisy系统的优越性。

通过一系列调研和考证，我们设计了两种对比，以证明持久化机制性能方面的提升。

## 实验评估

### 实验环境

实验使用NVDIMM（SUPERMICRO）服务器模拟NVM。
具体各项如下：
- CPU：Intel Xeon E5-2643 v3 3.40GHz 
- 内存：16GB
- 硬盘：300GB
- 操作系统：CentOS 7
- 网络：局域网（千兆）
- Benchmark: 标准 Hashmap 随机插入测试，Redis 内存数据库插入测试

实验采取两套benchmark，一套是hashmap的随机插入和删除测试，另一套是Redis的KV数据写测试。

基本实验设计图如下所示：

![持久化测试框架](http://kaixinhuang.com/DDST-NVM/img/MM-exp-design.png)

这里我们要着重区分 Daisy 系统的 NVM 持久化机制与传统的文件系统持久化机制：
- NVM 持久化：混合内存管理架构下，应用程序为需要持久化的数据（结构）调用持久内存分配接口，并通过内存事务接口保证数据一致性。这样一来，每次持久数据的写入都是直接存储在 NVM 中，因此具有非易失性，持久化过程就完成了。在实验中，我们使用 NVDIMM （加电 DRAM ） 同时模拟 DRAM 和 NVM，DRAM 用于临时数据的空间分配， NVM 用于持久数据的空间分配。

- 文件系统持久化：文件系统的持久化机制有两种，一种是通过 fread/fwrite 接口将数据读入或写入文件缓冲区，而持久化过程则是通过 fsync 操作来强制缓冲区中的数据刷到磁盘上；另一种是通过 mmap 接口将文件映射到内存，对内存的读写就相当于是对文件的读写，而持久化过程则是通过 msync 操作来强制内存中的数据写入到文件中。由于后一种方法具有相对更优越的性能，我们将其设定为比较的持久化机制。

为了保证实验的公平性，即最后实验结果所表现出的性能差异是由持久化机制所引发而不是跟存储介质相关，我们统一使用 NVDIMM 展开实验。对于文件系统持久化而言，我们将 NVDIMM 划分为两个部分——DRAM 和 RAMDISK （通过tmpfs实现），RAMDISK 可以理解为是具有 DRAM 读写速度的持久化 DISK。由于 NVDIMM 本身保证了非易失这一特点，用其模拟的 RAMDISK 也就满足了 DISK 的非易失特性。

由于 DRAM、NVM 以及 RAMDISK 三者均由 NVDIMM 模拟，实验就具备了公平公正的基础。根据后续 Benchmark 的测试所产生的性能差异，则全系于持久化机制的不同。

### Hashmap Benchmark 测试

### Hashmap Benchmark 简介

哈希表，是根据键（Key）而直接访问在内存存储位置的数据结构。也就是说，它通过计算一个关于键值的函数，将所需查询的数据映射到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做散列函数，存放记录的数组称做散列表。

本次实验中使用的 Hashmap 插入测试中： 我们写了一个简单的Hashmap 测试程序，测试过程中在哈希表中随机插入 1000 到 10,000,000 条键值对不等，，每个键值对大小为8 Byte （2 个 4byte 整型数据），测试结果统计完成指定数量键值对插入并持久化所耗费的时间。


#### Hashmap 随机插入 实验程序（部分）：

应该不需要，待定~


#### 结果及分析

|  测试数据量   |   持久化方式  | 耗时（ms） |
|:-:|:-:|:-:|
| 1000 | NVM Store | 0.012 |
| 1000 | 文件系统sync机制 | 0.186 |
| 10,000 | NVM Store | 0.125 |
| 10,000 | 文件系统sync机制 | 1.641 |
| 100,000 | NVM Store | 1.182 |
| 100,000 | 文件系统sync机制 | 17.864 |
| 1,000,000 | NVM Store | 13.2 |
| 1,000,000 | 文件系统sync机制 | 165.86 |
| 10,000,000 | NVM Store | 172.93 |
| 10,000,000 | 文件系统sync机制 | 1697.8 |

（由于时间差异比较悬殊，作图时可考虑对数折线图、柱形图，并且可以作归一化处理以防时间问题）



### Redis Benchamrk 测试

#### Redis Benchmark 简介

Redis 是一个使用 ANSI C 编写的开源、支持网络、基于内存、可选持久性的键值对存储数据库。

Redis 通常将全部的数据存储在内存中，目前通过两种方式实现持久化：
- 使用快照，一种半持久耐用模式，不时的将数据集以异步方式从内存以 RDB 格式写入硬盘。
- 使用 AOF 文件，一种只能追加的日志类型。它通过将数据集修改操作记录起来以保证持久性。Redis 能够在后台对只可追加的记录作修改来避免无限增长的日志。

本质上，Redis 的持久化也是文件系统持久化的类型，内部调用了 fsync 函数进行数据同步。但由于其属于典型的大数据应用（内存数据库），且具有相对成熟的持久化方法。如果使用 NVM 持久化机制，则需要对其数据存储和持久化模块作相应的修改，具体表现为：
- 第一，将 KV-pair 的存储区域改为 NVM （通过调用持久化内存分配接口可以实现）
- 第二，禁用 RDB 和 AOF 持久化。

同理，为了保证实验对比的公平公正性，我们依旧采用同前一实验相同的原则：统一使用 NVDIMM 模拟 DRAM、NVM 和 DISK （RDB和AOF均是将持久化快照、日志写到磁盘中）。

Redis Benchmark 基本介绍如下：

```
Usage: redis-benchmark [-h <host>] [-p <port>] [-c <clients>] [-n <requests]> [-k <boolean>]

 -h <hostname>      Server hostname (default 127.0.0.1)
 -p <port>          Server port (default 6379)
 -s <socket>        Server socket (overrides host and port)
 -a <password>      Password for Redis Auth
 -c <clients>       Number of parallel connections (default 50)
 -n <requests>      Total number of requests (default 100000)
 -d <size>          Data size of SET/GET value in bytes (default 2)
 --dbnum <db>       SELECT the specified db number (default 0)
 -k <boolean>       1=keep alive 0=reconnect (default 1)
 -r <keyspacelen>   Use random keys for SET/GET/INCR, random values for SADD
  Using this option the benchmark will expand the string __rand_int__
  inside an argument with a 12 digits number in the specified range
  from 0 to keyspacelen-1. The substitution changes every time a command
  is executed. Default tests use this to hit random keys in the
  specified range.
 -P <numreq>        Pipeline <numreq> requests. Default 1 (no pipeline).
 -q                 Quiet. Just show query/sec values
 --csv              Output in CSV format
 -l                 Loop. Run the tests forever
 -t <tests>         Only run the comma separated list of tests. The test
                    names are the same as the ones produced as output.
 -I                 Idle mode. Just open N idle connections and wait.
```

> 典型测试用例：redis-benchmark -t set -r 100000 -n 1000000

> 关闭 Redis 服务器: redis-cli shutdown

#### Redis KV-pair 数据写入 实验程序（部分）：

应该不需要，待定~

#### 结果及分析


|  测试数据量   |   持久化方式   |  吞吐量（rps） | 耗时（s） |
|:-:|:-:|:-:|:-:|
| 1000 | NVM | 142857.14 | 0.01 |
| 1000 | RDB | 142857.14 | 0.01 |
| 1000 | AOF | 11111.11 | 0.09 |
| 10,000 | NVM | 92951.22 | 0.11 |
| 10,000 | RDB | 114942.53 | 0.09 |
| 10,000 | AOF | 11976.05 | 0.83 |
| 100,000 | NVM | 132590.05 | 0.78 |
| 100,000 | RDB | 133155.80 | 0.75 |
| 100,000 | AOF | 11133.38 | 8.98 |
| 1,000,000 | NVM | 138811.77 | 7.20 |
| 1,000,000 | RDB | 130327.12 | 7.67 |
| 1,000,000 | AOF | 11188.31 | 89.38 |
| 10,000,000 | NVM | 196317.09 | 50.94 |
| 10,000,000 | RDB | 129320.93 | 77.33 |
| 10,000,000 | AOF | 12168.13 | 813.24 |

注：RDB之所以远远快于AOF是因为RDB是异步生成的快照，且具有较长的时间间隔，而AOF则是在 critical path 上的日志添加操作。
注：数据量小于 1,000,000 时之所以 NVM 持久化机制弱于 RDB，是因为完成总事务请求所耗费的时间还没达到快照记录的时间点，而 NVM 持久化存在更多内存分配和一致性保障方面的开销。但可以看出开销影响并不明显。当数据量较大时，NVM 持久化具有明显优势，尤其是与 AOF 相比（两者的数据持久化等级相同，RDB是粗粒度持久化）。

分析部分可参考 [Redis 持久化](http://redisdoc.com/topic/persistence.html)。


<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->



