---
layout: page
title: 情系 863 之名家讲堂
subtitle: Storage and Architecture for Next Generation Data Centers
use-site-title: true
---

## Abstract

![](storage-arch-next-generation-data-center.jpeg)

## Presentation

Prof. Murali Annaveram (Paper Received by MICRO'17)

### Introduction

Big Data Era and Memory Scaling

Storage Scaling

Fetching data from disk is a huge problem

(But many applications are) Stuck with block-based storage 

Semantic Storage
- Expose structure of data to storage controller
- Opportunities: Underutilized processing power
- Limitations: 
	- Wimpy embedded core
	- Dynamic processor usage
	- In-SSD computation should be utilized dynamically

What kind of work can be done?

One Scenerio: Flash-Summarizer
- Two options explored:	
	- Intra-query offloading
	- Inter-query offloading

Flash Summarizer Architecture
-  Firmware architecture

Algorithmic Flow
- Initiation: Transfer a in-SSD procedure to SSD memory
- Computation: Return the special code if the requested page is processed in SSD 
- Finalization: Gather final in-SSD computation results and transfer to the host

Detailed View
- Task Queue (TQ)
- Task Controller
- User function stacks

Application
- Data integration: String Similarity Join: Prefix filtering and Verify

Evaluation
- Platform: Multicore ARM processors + FPGA platform



<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" src="http://v2.uyan.cc/code/uyan.js"></script>
<!-- UY END -->



