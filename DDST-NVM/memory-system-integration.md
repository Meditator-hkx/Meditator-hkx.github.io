---
layout: page
title: 情系 863
subtitle: 内存管理系统集成之关键问题思考
use-site-title: true
---

## 关于系统集成的任务理解

### Intel 的 NVML 框架参考

![](http://www.kaixinhuang.com/DDST-NVM/img/system-integration/intel-nvm-swarch.jpg)

![](http://www.kaixinhuang.com/DDST-NVM/img/system-integration/intel-nvm-libarch.jpg)

### 我们的解决方案

#### 大数据应用方面的思考

什么类型的应用需要用到持久内存管理来运行，保证数据持久化和一致性而不是用文件系统的机制？

比如说 Redis KV-Store 应用，

## 一致性的非易失内存管理框架

我们内存系统的本身框架图可以构建为如下所示：






## 需要集成的功能模块简介

由于集成的 Linux 内核环境为 3.11, 而现在我们的系统使用的内核是 3.12. 因此排除掉 3.12 内核本身的代码部分，是需要提取并整合到新的 3.11 中的。所有更改的代码可以分为以下几个模块：

- 内核管理模块
	- 地址切分模块：涉及 SCM 地址的切分和 PFN 的变更
	- 系统调用模块：新增系统调用的映射关系
	- 匿名映射模块：主要负责 p_mmap 函数的实现
	- SCM 管理模块：新增代码的主体
	- 事务日志模块：主要实现了 log 和 transaction，用于一致性的维护
- 用户模块：主要是指 pcmapi 所包含的全部代码及实验测试代码，此部分是独立于内核版本的