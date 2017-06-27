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
 - 所谓性能测试，即是对比Store数据直接到NVM的方式比传统的持久化方法（从内存持久化到磁盘的操作通常是fysnc或者msync），根据项目结题指标，性能提升应在5% 以上，实验设计图如下所示：
 
 ![持久化测试框架](https://github.com/Meditator-hkx/Meditator-hkx.github.io/edit/master/DDST-NVM/img/MM-exp-design.png)

## 基本简介

NVM是一种新型非易失存储器件，它兼具传统的DRAM和DISK二者的优势，可按字节读取数据，非易失，容量大。正因为它在持久化数据方面无与伦比的优势，我们考虑将其用作存储持久数据的空间。我们做了一套管理非易失内存的支持系统，它提供给用户一些使用NVM空间的接口，并且在操作系统层面上维护用户所使用的NVM空间。通过简单地Load/Store指令，用户程序就可以加载和存储需要持久的数据（或数据结构）。另外，由于DRAM本身的易失性，存储在其中的数据在程序故障或系统崩溃以后会全部丢失，但NVM中的数据却会一直存在。这就可能导致数据一致性方面的问题，因此除了持久内存的分配接口，我们也提供给用户一致性保障的接口，来保障系统持久化的正确性。比起传统的内存管理系统，我们设计的持久内存系统在持久化方面有更高的性能。下面我们将设计实验来证明我们的Daisy系统的优越性。

根据任务书性能指标，我们的 *持久内存系统的持久化机制* 需要比 *传统持久化机制* 在性能上提升**5%**以上。

通过一系列调研和考证，我们设计了对比实验，以证明持久化机制性能方面的提升，并且会针对结果给出相应的合理解释。

## 实验评估

### 实验环境

实验使用NVDIMM（SUPERMICRO）服务器模拟NVM。
具体各项如下：
- CPU：Intel Xeon E5-2643 v3 3.40GHz 
- 内存：16GB
- 硬盘：300GB
- 操作系统：CentOS 7
- 网络：局域网（千兆）

### 实验流程

实验采取两套benchmark，一套是hashmap的随机插入和删除测试，另一套是Redis的KV数据写测试。

Hashmap 随机插入 实验程序（部分）：

```
#include <stdio.h>

```

Redis KV-pair 数据写入 实验程序（部分）：

```
#include <stdio.h>

```

## 实验结果图

性能对比图需要两个：
一个是与传统的持久化机制对比的；另一个是Redis应用对比的。

![持久化测试结果](https://github.com/Meditator-hkx/Meditator-hkx.github.io/edit/master/DDST-NVM/img/MM-exp-re-1.png)
![持久化测试结果](https://github.com/Meditator-hkx/Meditator-hkx.github.io/edit/master/DDST-NVM/img/MM-exp-re-2.png)



远远超过5%，如何解释这种现象？

解释：

## 总结



