---
layout: page
title: 内存数据库中的索引结构
subtitle: 道可道，非常道
use-site-title: true
---

## 数据库中的常见索引结构

索引对于加快对数据库中的数据的访问具有非常重要的意义。上一篇文章 [数据库中的 Index](http://kaixinhuang.com/TechBlog/Blogs/Database/what-is-an-index/) 重点介绍了基本的索引 Index 的概念和工作流程，今天我们对应用在数据库中的主流索引结构进行更深一步的探究。

### Array Index

数组是一种非常简单的数据结构，曾在 IBM 的 OBE 项目中被采用作为索引结构。

数组结构时最节约空间的，但它有两个弊端：其一是在建立索引时必须确定空间大小，不利于动态扩展（当然，这可以通过底层的mmap机制来使用虚拟内存进行扩展，但空间一开始必须分配地足够大）；其二是每次更新操作的时间复杂度都是O(N)，这就非常不利于实际工作中非只读事务了。

因此，在很长一段时间内，array index 在工业界都不再被使用。

### AVL Tree Index

AVL 是一种自平衡的二叉树，其命名是源自两位作者的名字首字母组合：Adelson-Velsky & Evgenii Landis. 它的数据结构图如下所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/AVL-Tree.jpg)

它是一种基于内存设计的索引结构，在索引上具有log(n)的时间复杂度，但缺点是空间开销太大，一个节点只保存一个有效数据（其他是指针数据）。

### B-Tree Index

B-Tree 及其 变种 B+-Tree 是广泛应用于磁盘数据库的索引结构，近些年来的内存数据库（比如 H-Store）也常使用它们作为基本索引结构来来提高 range query 的性能。

B-Tree 的基本结构如下所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/B-Tree.jpg)

与 AVL-Tree 相比，B-Tree 在磁盘数据库上更具优势，但在纯内存环境下（如果内存足够大）性能就不如了。

### B+-Tree Index

B+-Tree 与 B-Tree 其实非常相似。它们的区别在于，B+-Tree 的中间节点不保存数据值，只保存到下一层的指针。全部的数据值都保存在叶子节点中，且叶子节点相互连接成（单向或双向）链表，检索和更新操作非常便利。两者的区别图示如下：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/B+-Tree.jpg)

B+-Tree 结构更适合磁盘数据库，而 B-Tree 更适合内存数据库。

### Chain Bucket Hashing Index

而在所有的索引结构中，单条目索引在哈希结构中是最快的，因为哈希索引的索引方式为 loc = hash(key)。一个 Chain Bucket Hash Index 数据结构如下图所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/hash-chained.jpeg)

映射到同一位置的哈希键可以组织成一个 list bucket，解决哈希冲突的问题。然而，这一哈希索引结构的问题是它是静态结构（这也是它的优点），事先必须决定哈希表的大小——当然，这并不等于哈希键的映射数量是被限制的。只是在扩展时这一索引结构不太方便。

### Extendible Hashing Index

这一哈希索引结构解决了上一结构的问题，可以事先动态扩展，它的数据结构如下图所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/hash-extendible.jpeg)

扩展的原理是利用类似哈夫曼编码中的最小前缀做区分，具体的例子可以参考相关链接中的图示。

### Linear Hashing Index

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/hash-linear.jpeg)

### Modified Linear Hashing Index

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/hash-modified.jpeg)

### Others
- Skiplist Index
- Radix Tree Index
- MassTree Index

## 一种新型的索引结构：T-Tree

### T-Tree 的数据结构组成

T-Tree 是一种结合 AVL-Tree 和 B-Tree 两者特色而诞生的索引结构，既保留了 AVL-Tree 面向内存、快速检索的特性，又保留了 B-Tree 高度支持范围查询、节省空间的特性。

T-Tree 的数据结构如下所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/T-Tree.jpg)

T-Node 的结构如下所示：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/T-Node.jpg)

### T-Tree 的操作
- 查找
- 插入
- 删除
- 平衡（自动）

这些动作兼具 AVL-Tree 与 B-Tree 的特点，没有太大的特殊性，但在之后的实验性能表现上确实更佳。

### T-Tree 的利弊分析

T-Tree 更适合 OLAP 还是 OLTP？答案是 OLAP。理由呢？

H-Store 选择 B+-Tree 作为 Index 结构的理由是什么？



## 实验评估：数据结构对数据库性能的影响

实验采用了一系列 transaction workload 的组合：建立数据库+索引表，首先是搜索，然后执行三种 query 的组合，然后执行 range queries， 然后执行 table scan， 最后删除一半数据。

- 搜索：
- 三种 query 组合：
- range queries：
- table scan：
- 删除： 

实验结果如下：

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/exp-1.jpeg)

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/exp-2.jpeg)

![](http://kaixinhuang.com/TechBlog/Blogs/Database/Index-img/exp-3.jpeg)


相关链接：
- [1986 VLDB: A Study of Index Structures for Main Memory Database Management Systems]()
- [Chain Bucket Hashing Index](http://opendatastructures.org/ods-cpp/5_1_Hashing_with_Chaining.html)
- [AVL-Tree 维基百科](https://en.wikipedia.org/wiki/AVL_tree)
- [B-Tree 及 B+-Tree 的区别](https://stackoverflow.com/questions/870218/differences-between-b-trees-and-b-trees)
- [Extendible Hashing 维基百科](https://en.wikipedia.org/wiki/Extendible_hashing)
- [Linear Hashing 维基百科](https://en.wikipedia.org/wiki/Linear_hashing)


<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->