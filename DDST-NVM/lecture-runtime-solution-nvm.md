---
layout: page
title: 情系 863 之名家讲堂
subtitle: Runtime Solutions to Apply Non-Volatile Memories in Future Systems
use-site-title: true
---

## Abstract

Main memory has been the performance and energy bottleneck of almost all computer systems. Currently, DRAM as the mainstream in main memory technology is experiencing many technical challenges in its capacity, energy-efficiency, and reliability. With the emergence of technologies, a new path to address the limitations of DRAM has appeared.

In this talk, the applicability of two of these technologies, namely PCM and DWM as a replacement of DRAM is explored. Alongside their benefits, PCM suffers from limited write endurance, long write latency, and high write energy, while the main challenge to employ DWM is due to its sequential access structure which requires shift operations to align to the data of interest to the access point. Here, three different solutions are proposed to either PCM-based main memory, first a wear-resistant page allocation algorithm is proposed, then a conflict miss aware page allocation algorithm is proposed for a hybrid DRAM-PCM main memory. At last, the latency of page table accesses in a DWN-based main memory is reduced using a pre-shift scheme which, based on the state of each entry in page table, pre-aligns the access port to hide read and write latency.

## Presentation

Chengmo Yang

### Introduction

In different computer systems, DRAM is always one of the most important parts.

Research point: performance -> Performace / Cost 性价比

DRAM Problem: DRAM scaling is lower than Moore's Law and a large amount of energy cost

替代DRAM的两种考虑：
- 集成度 
- 访问延迟
 
PCM 的弱点
- 写穿透（加热）
- 耗能

DWM 的特点
- 读写速度快
- 非易失
- 与 STT-RAM 相像
- Sequential Access Structure (缺点，顺序访问)

### Prolonging PCM Limited Lifetime

PCM Cell Structure

**晶态到非晶态，非晶态到晶态** 两种操作并不对称

频繁的加热导致材耗问题（写操作）

基本方法：
- Write Reduction (At bit level, reduce average writes)
- Write Balancing (Balances writes, at different granularity levels)
- Defect Tolerance (Maintain a list to keep the available memory blocks)

Write balance 可以以不同的粒度进行

- Fine grained
- Coarse grained

地址映射 - Page Table 

对物理页进行分类：old / young

对虚拟页进行分类： cold / hot

三个问题：
- 如何记录物理页的老少？对所有页加上访问计数器。
- 虚拟页的冷热如何确定？不需要加计数器，overhead 比较大，根据虚拟内存分段机制，TEXT 段存储代码不执行写操作，DATA 段和 DSS 段属于热页。这是自然的想法。
- 什么时候去交换（改变映射）？Wear-leveling can be done across different processes (No extra remaps)

补充：定义两个阈值，确定冷热的限制。

解决了这三个问题，这一个工作基本上就完成了。

关于实验：跑许多个 trace 和 benchmark 跑到一个 page 死掉，看总共的写操作。标准化写寿命比。

问：怎么确定一个页写穿了？


### Hybrid Architecture with DRAM and NVM

两种架构
- 并行架构 （不重合的地址空间，读写操作的分离），NVM 很大，缺点是能耗瓶颈
- 层级架构 （DRAM 作为 NVM 的缓存），两步访问 miss 会导致访问比较慢

他们选择层级架构，希望解决 Hit miss 时的延迟问题。

Related Work:
- Clean First Replacement
- Miss Penalty Reduction

Page miss 是由 conflict 导致的，但是其实是很不平衡的。解决方案是使 miss 的分布更加平衡？应该是减少 miss 吧。

通过改变页到 set 的映射，从而减少竞争所导致的 miss。

A counter per set is used (两位的饱和计数器，计数从 0 到 3)

OS 的替换算法：一维到两维

问题：Observation 部分， set 的概念由何而来？

### Reducing DWN Access Latency

解决 Page Table 结构在 DWM 中如何实现。

每个 track 上有不同的 Domain，配置端口进行读写。

访问最远和最近的数据的延迟是不同的，传统的方案是使得想要取得的数据距离端口比较近。

Related Work: 
- 材料研究（Nature）
- 使用 DWM 做 Register / Cache
- 用 DWM 做 Main Memory
- 用 DWM 做 Storage

Page Table 用来做虚拟地址到物理地址的映射。如果虚拟地址很大（通常是48位，四级页表），页表存在内存里。四级页表也在内存里，这是一个需要解决的问题。

其实页表就相当于数据库的Index，50%的访问其实是花在页表上？

三种类型:
- Not in Memory
- In TLB
- In Memory but not in TLB

对 Distribution of shifts 进行实验观察，从而得出可以减少的非必要操作。

页表页(PTPage)的放置问题 （回去需要继续调研下这个问题，学习页表相关知识）

问题：关于实验环境


### Conclusion

两种物理器件

三个技术提升



<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->



