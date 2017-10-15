---
layout: page
title: 分布式持久内存文件系统的研究准备
subtitle: 雄关漫道真如铁，而今迈步从头越
use-site-title: true
---

## DDST持久内存文件系统工作概览

|项目名|论文Title|发表期刊/会议|工作简介|
|:-:|:-:|:-:|:-:|
|HMFS|HMFS: A Hybrid Memory File System with Version Consistency|SoCC poster|一种新型混合平行架构（NVM+DRAM）的内存文件系统，使用mmap来创建Load/Store接口，利用版本来维护文件一致性|
|/|A Consistency Mechanism for NVM-Based in-Memory File Systems|CF|一种新型的维护基于NVM的文件系统的一致性方案，使用惰性确认的快照策略来获得高一致性保障|
|HMVFS|HMVFS: A Hybrid Memory Versioning File System|MSST|一种基于HMFS开发的支持多版本快速回退的混合架构（NVM+DRAM）内存文件系统|
|WARP|Adaptive Prefetching for Accelerating Read and Write in NVM-based File Systems|ICCD|感知文件系统中的一种自适应预取算法模块，主要为了解决由于NVM读写性能不对称时文件读写负载不均衡所导致的性能下降问题|
|Versioning-mmap|Versioning-mmap: A DAX-enabled Mmap Mechanism for Versioning In-memory File Systems|IPCCC|一种新型的mmap机制，主要用来解决基于DAX的内存文件系统维护一致性（COW方式）时的严重开销问题|


## 基于NVM的内存文件系统相关工作

PMFS...SCMFS...BPFS... NOVA ... 总体介绍一遍


## 分布式持久内存文件系统的研究动机

我们为什么希望设计一种**分布式**的内存文件系统呢？

现在的持久内存文件系统有什么问题？

NVM+RDMA对于持久内存文件系统有什么样潜在的巨大优势？


## 分布式内存文件系统的机遇与挑战

NVM的非易失特性

RDMA的分布式优点

分布式场景下的问题变化？

NVM所带来的新问题？

## 补充与扩展

现有哪些不错的RDMA工作可以借鉴？什么技术？什么效果？如何运用到内存文件系统的场景中来？如何与NVM完美地结合在一起？



<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->
