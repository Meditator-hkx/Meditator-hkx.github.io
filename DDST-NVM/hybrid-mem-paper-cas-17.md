---
layout: page
title: Memo 现代操作系统中的混合内存管理模型
subtitle: 另一种思路
use-site-title: true
---

## 题目解读
- 现代操作系统：在涉及操作系统方面的工作时，很少有论文会专门去加上“现代”这个限定词，而之所以加在前面，是因为研究者想强调区别与传统操作系统的
内存子系统架构（比如多 channel 多 rank 布局）
- 混合内存管理：进一步限定了工作对象，是对 DRAM 及 NVM 共同作为内存的管理。NVM 的产生带来了新的机遇和挑战，因此混合内存管理有相当的必要性。
- Memos: 工作成果——一种基于 DRAM/NVM 的特点针对性设计与实现的混合内存管理的框架。

## 工作简介

他们开发了 Memos 作为一种新时代的混合内存管理框架，将NVM 与 DRAM 统一到多通道平行架构(MCHA)，主要针对 NVM 的高读写延迟、读写性能不对称、易写穿所带来的问题设计了相应的解决方案，
包括负载预测、内存占用检测与冷热页迁移等。
比起传统的解决方案（state-of-the-art），取得了比较显著的效果。

## 背景与挑战

新型存储器件技术如PCM、RTT-STM等为设计大型非易失存储器提供了可能，NVM将与DRAM一同作为内存纳入统一的地址管理。此时有两种做法，层次架构和平级架构。
层次架构不能很好地发挥 NVM 的自身优势，而平级架构则有更光辉的发展未来。

而（研究者认为）挑战主要是来自 NVM 自身存在的较高的读写延迟和读写不均衡及耐久性问题。因为这些问题的存在（实际上非易失作为优势的同时也带来了新的问题
，比如数据一致性问题），传统的内存管理架构就不适于新型的 NVM 内存管理，需要做一定程度的合理的改良优化。比如智能地为应用分配 DRAM 或 NVM 内存页，
监视内存使用，进行冷热页迁移等等。


## 关键技术

### 内存读写访问的研究

研究者们为了使混合内存管理架构在性能上有所提升，针对内存访问模式做了专门的研究。

其中，读写访问模式如下图所示

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/access-pattern.jpeg)

基于读写负载的历史信息进行预测的观察如下图所示

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/access-prediction.jpeg)

预测机制的设计如下图所示

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/predict-model.jpeg)


内存的重用模式上也存在巨大的差异，尤其以 GemsFDTD 负载为主，如下图所示

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/reuse-pattern.jpeg)


### 内存利用率的监视

由于内存子系统层级结构方面的特殊性，传统的方法没有考虑存在内存读写性能不对称时的内存分配优化，如下图所示：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/bank-utilization.jpeg)



## Memos 设计与实现

Memos 的基本框架如下图所示：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/memos-framework.jpeg)


NVM 纳入 Buddy System 统一管理，其子系统图如下：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/nvm-buddy.jpeg)


内存页在 DRAM 和 NVM 间的迁移算法如下图所示：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/page-migration.jpeg)


## 实验评估

实验的仿真框架如下图所示：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/emulation-platform.jpeg)

实验结果图如下所示：

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/exp-resource-schedule.jpeg)

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/exp-latency-energy.jpeg)

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/exp-bank-opt.jpeg)

![](http://kaixinhuang.com/DDST-NVM/img/hybrid-mem-paper-cas-17/exp-resource-schedule.jpeg)


## 总结与思考

待补充

研究者干了一件什么事？
怎么干成的？有哪些干货性的设计或技术？
意义、影响或者是可观的效果？

我觉得问题存在于哪里？在一致性方面的讨论几乎没有看到——但实际上NVM的确会带来一致性问题。如果不加以解决，根本就无法保证系统的正确运行。是否轻量级应该
是我们的主要卖点。

还有就是向下相关，真的有那么相关？可移植性真的很强？如何识别垂直化的内存子系统资源？多少rank，多少chip，多少bank等等。。。

可用性的依据？workload的变化是否合理？如何识别严重的 read-heavy 或者 write-heavy workload，页迁移的基础是否合理，迁移的开销？

<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->

