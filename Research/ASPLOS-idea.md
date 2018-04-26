---
layout: page
title: Give a Try to ASPLOS
subtitle: EFLightPM: An Efficient, Friendly and Lightweight Persistent Memory Library for Fine-grained Data
use-site-title: true
---

## Key Contributions

1. Fine-grained persistent data managment is a significant problem and we develop easy-to-use (user-friendly and flexible) APIs for data allocation, recycling and managemnt.
2. We design our management mechanism with real hardware condition (but not an assumption), and especially we integrate our system with App-Direct Mode (of Intel Appache Pass).
3. We design a hierarchical hash-based allocation mechanism and a workload-aware logging scheme in EFlightPM to reduce metadata overhead thus make efficient persistence.
4. We are the first to conduct extensive experiments on a real NVM platform, and the results show that

## Introduction

## Background & Motivation

## Design & Implementation

## Applications

## Experiments & Analysis

## Related Work

- Mnemosyne (Read it again and understand how it fulfills the fine-grained data management)
- NV-Heaps (Understand the memory abstraction)
- HEAPO (Undertsand the minimal logging mechanism)
- libpmemobj (Understand the pmalloc interface based on file system)
- write behind logging (Peleton)

## Conclusion

## References
