---
layout: page
title: Database Paper Notes
subtitle: Reducing the Storage Overhead of Main-Memory OLTP Databases with Hybrid Indexes Huanchen
use-site-title: true
---


## One-shot Read Conclusion

### Abstract

Three Contributions:
- Introduction of hybrid indexes
- Dual stage transformation (DST)
- Applying DST into four order-preserving index structures

### Introduction

What kind of story does it tell?

1. Main Memory DBMS is prevailing at this moment with high throughput and low latency with holding the whole work set in memory.

2. It is important to improve the memory efficiency for main-memory DBMS with 2 reasons: saving storage money (fixed workset) and loading more data in memory (data beyound memory capacity).

3. In such a background, index structure cosumes a lot of memory (almost 55% in H-Store, which is the state-of-the-art memory-oriented DBMS). However, simply eliminating all the indexes can be suboptimal.

4. We propose hybrid index structure and we are the first to apply it in this area.

5. The dual storage artchitecture is good with low latency and high throughput.

6. It leverages the skew character in OLTP workloads.

7. To build such a hybrid index, we develop DST method and apply it to four order-preserving index structures and integrate them with H-Store and find it good in performance - 70% reduction in memory usage.

8. We make four kinds of contributions:
- three in Abstract
- the opportunity to use compact/compressed static data structures

### Conclusion

What kind of story has been told?

1. What is hybrid index? It uses dual-stage architecture to combine two data structures with different optimization emphasis to create a memory-efficient order-preserving index.

2. How two stages differetiate iteself from each other? Dynamic stage uses a fast dynamic data sturcture for operations on recently addes entries and a compact, read-optimized data structure for colder data. 

3. Why does DST occur and how does it work? It acts as a set of guidelines to help convert any order-preserving index to hybrid index.

4. What's the result of experiment? Comparable throughput and significant less memory consumption.

5. What's the inspiration of such work? To motivate the hybrid index design in main memory DBMS.

### Background: THE CASE FOR HYBRID INDEXES

What kind of background is offered by the author? And are you persuaded?

1. Indexes are a major factor in the memory printfoot of a database.

2. It is important to design a memory-efficient index structure to improve the performance and reduce the cost.

3. A common way to reduce the size of B+ trees is to compress their nodes before they are written to disk, at the price of a heavy decompression overhead for OLTP workloads.

4. One question is: should index treat all the data tuples in the underlying tables equally?

5. For many OLTP applications, it shouldn't behave that way (for example, the news data in database).

6. So, a possible solution might be to build **multiple partial indexes[^1] on the same keys with different data structures**.

7. However, partial indexes with different data structures has its own problems:
- Developers might need to modify the applications to decide for certain data tuples, what kind of indexes they should be stored as.
- Query Optimizer also doesn't know what index to use for a query.

8. A better and novel approach can be simple hybrid index structure with dual-stage architecture.

9. Index entries for new tuples go into a fast, write-friendly data structure since they are more likely to be queried again in the near future. Over time, the tuples become colder and their access patterns change, usually from frequent modification to occasional read.

10. The dual-stage architecture is shown as below:

![](http://kaixinhuang.com/Research/hybrid-index/dual-stage.png)

Explanation: 

- Two stage index structure
- DTS Rule
- Bloom Filter
- Order of Access

The pratical example of dual-stage architecture on four index structures are as follows:

![](http://kaixinhuang.com/Research/hybrid-index/multi-index-struct.png)



### Discussion = Experiments and Result Analysis

![](http://kaixinhuang.com/Research/hybrid-index/microbench-DTS-test.png)
![](http://kaixinhuang.com/Research/hybrid-index/microbench-performance.png)
![](http://kaixinhuang.com/Research/hybrid-index/systest-in-mem-workload.png)
![](http://kaixinhuang.com/Research/hybrid-index/systest-larger-than-mem-workload.png)


## Technique Details

- How DTS is implemented?
	- Compaction
	- Reduction
	- Compression
- How static index structure is implemented?
- Merge Process
	- Blocking
	- Merfe-all



## Inspired Ideas
- A Tradeoff Research among Data Tuples, Indexes and Evicted Tables in Memory Consumption
- Dual-stage Architecture of Indexes in Hybrid Memory
- A Memory-efficient Hash Table Index with Range Query Optimization
- Non-blocking Merging for Hybrid Indexes with COW




[^1]: Also called filtered index, which is built on only a few data rows that satisfy specified conditions. Details can be found in [Wiki](https://en.wikipedia.org/wiki/Partial_index)

<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->


