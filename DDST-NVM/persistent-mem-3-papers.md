---
layout: page
title: 持久内存系统设计的三座大山
subtitle: Mnemosyne, NV-Heaps与Heapo
use-site-title: true
---

## 持久内存系统设计简介

持久内存系统提供一种通过Load/Store指令来以字节寻址的方式直接访问持久内存（PM）的接口，而不是通过文件系统的形式使用持久内存。其中，Mnemosyne, NV-Heaps与Heapo 是早期设计的解除代表，前两者发表在 ASPLOS 上，后者发表在 TOS 上。Mnemosyne 提供了一套简便易用的持久内存使用接口，而 NV-Heaps 和 Heapo 提供了基于（常用数据结构）对象的内存访问接口，是更高一层的抽象和封装。

我们 DDST 内存管理组也在 863 内存计算项目的驱使下，也开发了一套名为 Daisy 的持久内存系统，主要实现了对 PM 的持久区域抽象、持久区域管理以及持久内存下的数据一致性保证。

然而，在将我们组的工作整理成一篇论文的过程中，我遇到了许多设计层面的问题：

## Introduction 模板

## 老师的模板

### Mnemosyne 风格

### NV-Heaps 风格

### Heapo 风格

### 我决定采用的风格



## Daisy：说个故事给你听






<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->

