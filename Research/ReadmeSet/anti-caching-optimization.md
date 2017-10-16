---
layout: page
title: Adaptive Eviction for In-Memory DBMS Anti-Caching
subtitle: Optimization on H-Store
use-site-title: true
---

## 摘要

内存数据库的发展如火如荼，将所有数据放在内存中处理，省去了许多软件层面的开销，提高了事务性能。。。

然而，当数据量超出可用内存时，内存数据库的问题就会暴露出来。。。

Anti-Caching的设计理念规避了这样的弱点，它主要是利用workload对数据库数据访问的冷热不均衡性质，将热的数据留在内存中，将冷的数据弹出到磁盘中。这样一来，就可以避免粗粒度的数据弹出与寻回以及造成系统暂停的对用户透明的虚拟页交换。

然而，Anti-Caching目前采用固定的参数配置，不具备灵活性。根据我们的实验观察，针对不同的workload特征，固定参数设置并不能满足高性能的需要。而人工调节参数费时费力，且很难取得理想的效果。

因此，我们提出了自适应数据弹出的设计，将其加入到Anti-Caching中，以期取得在任何时间段内，Anti-Caching都能表现出高性能的优势。

我们设计并实现了四组自适应调节方法，做了丰富的实验进行分析。通过实验我们得出，自适应调节后Anti-Caching的性能可以达到？到？倍的显著提升，并且。。。在xx的自适应调节方法下，Anti-Caching能够取得最佳效果。


## 背景

### 内存数据库 VS 磁盘数据库

### H-Store 概要

### Anti-Caching

#### 基本框架

#### 数据存储

#### 数据弹出

#### 事务执行

#### 数据回取


## 动机

### 实验观察：Anti-Caching固定参数设置的局限性

#### 同一YCSB Benchmark下的不同Eviction参数组性能对比

#### 同一Eviction参数组下的不同YCSB Benchmark性能对比

#### 同一Eviction参数组下YCSB Benchmark发生剧变时的性能对比

### 瓶颈分析

#### 对用户透明的虚拟页交换

#### 长数据弹出开销

#### 频繁的数据回取过程

### 典型案例

#### Anti-Caching功能启动

用户不应该对此负责。。。

#### 弹出阈值设计

阈值设置地过高过低可能出现的问题。。。

固定阈值可能存在的问题。。。

#### 弹出大小设计

弹出过大，浪费了空间容量。。。

弹出过小，容易超出内存。。。

#### 弹出检查设计

区间过小，CPU暂停开销增大。。。

区间过大，容易超出内存。。。


## Adaptive Eviction

### 基本思路

### 四种策略


## 实验设计

### Benchmarks
### 系统设置
### 实验结果与分析


## 总结与思考

我们所做的贡献可以归纳如下：

第一，观察出现有Anti-Caching的问题瓶颈所在并进行了深入的分析；

第二，提出了Adaptive Eviction的理念，且进一步在H-Store中设计并实现了四种策略；

第三，开展了丰富翔实的实验，我们的方案要比baseline提升xx倍，其中xx方法尤为出色，提升了xx倍，而空间开销则可忽略不计。




<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->
