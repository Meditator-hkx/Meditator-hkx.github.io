---
layout: page
title: Adaptive Eviction for Anti-Caching Based In-Memory DBMS
subtitle: How to Introduce Non-Volatile Memory (NVM) into Adaptive Anti-Caching
use-site-title: true
---

## DASFAA 2018 Paper Summary

[An Adaptive Eviction Framework for Anti-Caching Based In-Memory Databases](https://link.springer.com/chapter/10.1007/978-3-319-91458-9_15)

Current in-memory DBMSs suffer from the performance bottleneck when data cannot fit in memory. 
To solve such a problem, anti-caching system is proposed and with proper configuration, 
it can achieve better performance than state-of-the-art counterpart. 
However, in current anti-caching eviction procedure, 
all the eviction parameters are fixed while real workloads keep changing from time to time. 
Therefore, the performance of anti-caching system can hardly stay in the best state. 
We propose an adaptive eviction framework for anti- caching system and implement four tuning techniques to automatically tune the eviction parameters. 
In particular, we design a novel tuning technique called window-size adaption specialized for anti-caching system and embed it into the adaptive eviction framework. 
The experimental results show that with adaptive eviction, anti-caching based database system can outperform the traditional prototype by 1.2x-1.8x and 1.7x-4.5x under TPC-C benchmark and YCSB benchmark, respectively.

## Why Anti-caching DBMS needs NVM

Since the DRAM space is limited, the whole database may exceed the available memory size,
thus a few (cold) data tuples should be evicted to external storage. 

Anti-caching adopts disk as the anti-cache, which becomes a serious IO bottleneck.
With the emerging non-volatile memory, we can use much more access-efficient storage device.

Suppose cold tuples are organized and evicted into NVM, the bottleneck then occurs in the procedure of software design
of eviction block merging and cold tuple retrieval.

Then what other components should make a change for it?

The evicted table should make a change: it tracks the cold data not using block ID anymore, but the explicit NVM address.

For adaptive eviction, the problem is even tougher: how can the adaptive eviction framework be promoted exploiting the potential of NVM?

**ONLY REPLACEMENT IS FRA FRAM ENOUGH**



