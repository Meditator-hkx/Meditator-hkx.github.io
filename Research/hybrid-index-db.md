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


### Discussion = Experiments and Result Analysis


## Technique Details


## Pros and Cons -> Ideas






<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->


