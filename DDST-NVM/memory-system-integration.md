---
layout: page
title: 情系 863
subtitle: 内存管理系统集成之关键问题思考
use-site-title: true
---

## 关于系统集成的任务理解

### Intel 的 NVML 框架参考

![](http://kaixinhuang.com/DDST-NVM/img/intel-nvm-swarch.jpg)

可以看到，Intel 所设计的框架是将对 NVM 的内存访问模式，文件系统访问模式以及卷模式三者融合在一起的。

![](http://kaixinhuang.com/DDST-NVM/img/intel-nvm-libarch.jpg)

在接口库方面，Intel 提供了较为成熟的文件系统访问接口以及内存访问模式下的 pmalloc 接口、事务接口。

### 我们的解决方案

一个仿照 Intel 的设计所拟画的基本草图如下所示：

![](http://kaixinhuang.com/DDST-NVM/img/dual-integration-1.png)


#### 大数据应用方面的思考

什么类型的应用需要用到持久内存管理来运行，保证数据持久化和一致性而不是用文件系统的机制？

比如说 Redis KV-Store 应用，所有操作是在内存中进行，而持久化则是通过 RDB 快照（异步写回）或者 AOF 日志（追加log）实现的。

因此，既然 Redis 占用了内存空间，在加载磁盘数据到内存时势必以某种方式为其分配内存，而新的键值对的生成也必须占用新的内存空间。因此， Redis 显示或隐式地调用了 malloc 来分配内存空间。

若是要使用 NVM 对数据进行持久化，则需要满足两个条件：第一，NVM 与 DRAM (传统内存空间) 统一编址，纳入统一的内存管理框架；第二，需要类似 malloc 一样的接口来使得分配 NVM 空间成为可能。

这两个条件在我们的 Daisy 系统中得以满足。因此，用 p_malloc (分配持久内存的接口)来替换 malloc, 这样一来 Redis 所请求的内存就不再是传统的易失性内存，而是持久内存。键值对在这些内存空间中的数据写入如果满足原子操作（要么成功写入键值对整体，要么键值对均不写入）的特性，也就完成了持久化的目标。
从而， RDB 持久化和 AOF 持久化所需要的复杂漫长的数据同步就不再需要，大大节省了时间。

然而剩下的问题是，如果磁盘作为 Redis 数据后备的价值不再存在，那么数据只需要存在于 NVM 中即可，连数据的加载过程也可以省略。对于我们的 Daisy 系统而言，某个数据库只需要特定的ID就可以找到数据结构表并通过其特殊的数据结构（指针数据）来追踪其中的所有键值对数据。

即使是现在的不完善的 Daisy 系统，也可以完成上面提出的任务。

#### 内存管理与文件系统的结合

可能存在的不兼容之处：
- 内核版本的不同
- 内存地址的划分
- pmmap 的设计区别
- 内存资源的竞争
- 其他，待补充

## 一致性的非易失内存管理框架

我们内存系统 Daisy 的框架图可以构建为如下所示：

![](http://kaixinhuang.com/DDST-NVM/img/daisy-framework.png)

与传统的内存分配与管理并不相互矛盾而是兼容并包。

## 需要集成的功能模块简介

由于集成的 Linux 内核环境为 3.11, 而现在我们的系统使用的内核是 3.12. 因此排除掉 3.12 内核本身的代码部分，是需要提取并整合到新的 3.11 中的。所有更改的代码可以分为以下几个模块：

- 内核管理模块
	- 地址切分模块：涉及 SCM 地址的切分和 PFN 的变更
	- 系统调用模块：新增系统调用的映射关系
	- 匿名映射模块：主要负责 p_mmap 函数的实现
	- SCM 管理模块：新增代码的主体
	- 事务日志模块：主要实现了 log 和 transaction，用于一致性的维护
- 用户模块：主要是指 pcmapi 所包含的全部代码及实验测试代码，此部分是独立于内核版本的


## 周一组会的讨论

课题成果集成
- 多模式访问
- PM-Aware 文件系统
- PM 管理

Intel 的集成图

多模式访问接口

持久内存管理

建议：与因特尔的内存管理接口的区别是什么？怎么去描述 p_malloc 等的功能优越性。

Eurosys 论文中的 PM 的基本框图

System Software for Persistent Memory

最好还是用 Redis 作为大数据应用，比较有说服力，做实验也比较方便。

集成选择哪几家单位的模块？不是所有的模块都能集成。。。


<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->