---
layout: page
title: RDMA Architecture
subtitle: RDMA Learning Notes
use-site-title: true
---

## InfiniBand

InfiniBand (IB) is a high-speed, low latency, low CPU overhead, highly efficient and scalable server and storage interconnect technology.

InfiniBand provides various technology or solution speeds ranging from 10Gb/s (SDR) up to 56Gb/s (FDR) per port, using copper and optical fiber connections.

## Virtual Protocol Interconnect (VPI)

The Mellanox Virtual Protocol Interconnect (VPI) architecture provides a high performance, low latency and reliable means for communication among network adapters and switches supporting both InfiniBand and Ethernet semantics.

A dual-port VPI adapter, for example, can be config- ured to one of the following options:
- An adapter (HCA) with two InfiniBand ports
- A NIC with two Ethernet ports
- An adapter with one InfiniBand port and one Ethernet port at the same time

## RDMA over Converged Ethernet (RoCE)

RoCE is the most efficient low latency Ethernet solution today.

## Comparison of RDMA Technologies

Currently, there are three technologies that support RDMA: InfiniBand, Ethernet RoCE and Ethernet iWARP. All three technologies share a common user API which is defined in this docu- ment, but have different physical and link layers.

RDMA technologies are based on networking concepts found in a traditional network.

The key difference is that RDMA pro- vides a messaging service which applications can use to directly access the virtual memory on remote computers.

RDMA provides low latency through stack bypass and copy avoidance, reduces CPU utilization, reduces memory bandwidth bottlenecks and provides high bandwidth utilization.

In traditional sockets networks, applications request network resources from the operating system through an API which conducts the transaction on their behalf. 
However RDMA use the OS to establish a channel and then allows applications to directly exchange messages without further OS intervention. 
A message can be an RDMA Read, an RDMA Write operation or a Send/ Receive operation. IB and RoCE also support Multicast transmission.

The most important point is that every application has direct access to the virtual memory of devices in the fabric. 
This means that applications do not need to make requests to an operating system to transfer messages.

TCP/IP requires Operating System (OS) intervention for every operation which includes buffer copying on both ends of the wire.

TCP is responsible for retransmitting any lost packets due to congestion.

Once an application has requested transport of an RDMA Read or Write, the IB hardware segments the outbound message as needed into packets whose size is determined by the fabric path maximum transfer unit. 
These packets are transmitted through the IB network and delivered directly into the receiving application's virtual buffer where they are re-assembled into a complete message. 
The receiving application is notified once the entire message has been received.

## Key Components

### Host Channel Adapter

These are the equivalent of the Ethernet (NIC) card but they do much more. HCAs provide address translation mechanism under the control of the operating system which allows an application to access the HCA directly.

The application refers to virtual addresses while the HCA has the ability to translate these addresses into physical addresses in order to affect the actual message transfer.

### Range Extenders

### Subnet Manager

### Switches

They implement flow control of the IB Link Layer to prevent packet dropping, and to support congestion avoidance and adaptive routing capabilities, and advanced Quality of Service.
