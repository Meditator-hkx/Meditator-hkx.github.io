---
layout: page
title: Give a Try to JSS
subtitle: Daisy：An Efficient Memory System for Fine-grained Data Persistence
use-site-title: true
---

## Key Contributions

1. We underscore the heavy-overhead components in the design for fine-grained persistent data management in previous work;
2. We build a new persistent memory system 
3. We conduct extensive experiments on NVMDIMM platform. The result is really good.


## Intro

1. Give the background of NVM;

2. List previous work on persistent memory abstraction and utilization;

3. Analyze the weakness of previous researches one by one;

4. Introduce Daisy and how it avoids heavy overhead;

5. Introduce in details what we focus in Daisy;

6. List our contributions;

7. Give organizations.


## Background 

1. Review of Existing Persistent Memory Systems

2. Address Mapping Approaches

3. Logging Method

4. Motivation
  - fine-grained data requirement
  - allocation time delay
  - logging time overhead + logging metadata overhead
  
## Architecture

1. A framework figure

2. A programming library

3. Programming example and compare it with PMDK, Mnemosyne

## Design & Implementation

Based on previous version is okay.

## Applications

(Newly-added)

List two big data applications based on Daisy: TrajectoryStore and D-Redis.

## Experiments

Based on previous version is okay, but a few data should be updated since they might be problematic.

## Related Work

Based on previous version is okay and maybe a few more recent researches should be added.

## Conclusion 

It's mirror to Abstract.

