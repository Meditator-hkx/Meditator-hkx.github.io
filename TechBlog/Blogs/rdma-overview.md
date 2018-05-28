---
layout: page
title: RDMA-Aware Programming Overview
subtitle: RDMA Learning Notes
use-site-title: true
---

Mellanox provides a dynamically loaded library, creating access to the hardware via the verbs API. 

This programming manual and its verbs are valid only for user space. See header files for the kernel space verbs.

Programming with verbs allows for customizing and optimizing the RDMA-Aware network. 
This customizing and optimizing should be done only by programmers with advanced knowledge and experience in the VPI systems.

In order to perform RDMA operations, establishment of a connection to the remote host, as well as appropriate permissions need to be set up first. 
The mechanism for accomplishing this is the Queue Pair (QP).

For those familiar with a standard IP stack, a QP is roughly equivalent to a socket.

Once a QP is established, the verbs API can be used to perform RDMA reads, RDMA writes, and atomic operations.

## Available Communication Operations

### Send/Send With Immediate

The send operation allows you to send data to a remote QP’s receive queue. 
The receiver must have previously posted a receive buffer to receive the data. 
The sender does not have any control over where the data will reside in the remote host.

Optionally, an immediate 4 byte value may be transmitted with the data buffer.
This immediate value is presented to the receiver as part of the receive notification, and is not contained in the data buffer.

### Receive

This is the corresponding operation to a send operation. 
The receiving host is notified that a data buffer has been received, possibly with an inline immediate value. 
The receiving application is responsible for receive buffer maintenance and posting.

### RDMA Read

The caller specifies the remote virtual address as well as a local memory address to be copied to. 
The remote host must provide appropriate permissions to access its memory.
For both RDMA read and write, the remote side isn't aware that this operation being done (other than the preparation of the permissions and resources).

### RDMA Write / RDMA Write With Immediate

RDMA write operations are performed with no notification to the remote host. 
RDMA write with immediate operations, however, do notify the remote host of the immediate value.

### Atomic Fetch and Add / Atomic Compare and Swap

These are atomic extensions to the RDMA operations.

The atomic fetch and add operation atomically increments the value at a specified virtual address by a specified amount. 
The value prior to being incremented is returned to the caller.

The atomic compare and swap will atomically compare the value at a specified virtual address with a specified value and if they are equal, a specified value will be stored at the address.

### Transport Modes

There are several different transport modes you may select from when establishing a QP. 
Operations available in each mode are shown below in the table below (X means supported).
RD is not supported by this API.

| Operation | UD | UC | RC | RD |
| :-: | :-: | :-: | :-: | :-: |
|Send (with immediate) | X | X | X | X |
| Receive | X | X | X | X |
| RDMA Write (with immediate) |  | X | X | X |
| RDMA Read |  |  | X | X |
| Atomic: Fetch and Add/ Cmp and Swap |  |  | X | X |
| Max message size | MTU | 1GB | 1GB | 1GB |

### Reliable Connection (RC)

Queue Pair is associated with only one other QP.

Packets are delivered in order.

A RC connection is very similar to a TCP connection.

### Unreliable Connection (UC)

A Queue Pair is associated with only one other QP. 

The connection is not reliable so packets may be lost.

Messages with errors are not retried by the transport, and error handling must be provided by a higher level protocol.

### Unreliable Datagram (UD)

A Queue Pair may transmit and receive single-packet messages to/from any other UD QP.

Ordering and delivery are not guaranteed, and delivered packets may be dropped by the receiver. 
Multicast messages are supported (one to many).

A UD connection is very similar to a UDP connection.

## Key Concept

### Send Request (SR)

An SR defines how much data will be sent, from where, how and, with RDMA, to where. 

**struct ibv_send_wr** is used to implement SRs.

### Receive Request (RR)

An RR defines buffers where data is to be received for non-RDMA operations. If no buffers are defined and a transmitter attempts a send operation or a RDMA Write with immediate, a receive not ready (RNR) error will be sent. 

**struct ibv_recv_wr** is used to implement RRs.

### Completion Queue (CQ)

A Completion Queue is an object which contains the completed work requests (WR) which were posted to the *Work Queues (WQ)*. 
Every completion says that a specific *WR* was completed (both successfully completed WRs and unsuccessfully completed WRs).

A Completion Queue is a mechanism to notify the application about information of ended Work Requests (status, opcode, size, source).

CQs have n *Completion Queue Entries (CQE)*. The number of CQEs is specified when the CQ is created.

When a CQE is polled it is removed from the CQ.

CQ is a FIFO queue of CQEs.

CQ can service send queues, receive queues, or both.

Work queues from multiple QPs can be associated with a single CQ. 

**struct ibv_cq** is used to implement a CQ.

### Memory Registration

Memory Registration is a mechanism that allows an application to 
describe a set of virtually contiguous memory locations or a set of physically contiguous memory locations 
to the network adapter as a virtually contiguous buffer using Virtual Addresses.

The registration process pins the memory pages 
(to prevent the pages from being swapped out and to keep physical <-> virtual mapping).

During the registration, the OS checks the permissions of the registered block.

The registration process writes the virtual to physical address table to the network adapter.

When registering memory, permissions are set for the region. 
Permissions are **local write, remote read, remote write, atomic, and bind**.

Every MR has a remote and a local key **(r_key, l_key)**. 
Remote keys are given to the remote HCA to allow a remote process access to system memory during RDMA operations.

The same memory buffer can be registered several times 
(even with different access permissions)
and every registration results in a different set of keys.

**struct ibv_mr** is used to implement memory registration.

### Memory Window 

Memory Windows are intended for situations where the application:
- wants to grant and revoke remote access rights to a registered Region in a dynamic fashion 
with less of a performance penalty than using deregistration/registration or reregistration.
- wants to grant different remote access rights to different remote agents 
and/or grant those rights over different ranges within a registered Region.

The operation of associating an MW with an MR is called Binding.

Different MWs can overlap the same MR (event with different access permissions).

### Address Vector

An Address Vector is an object that describes the route from the local node to the remote node.

In every UC/RC QP there is an address vector in the QP context.

In UD QP the address vector should be defined in every post SR.

**struct ibv_ah** is used to implement address vectors.

### Global Routing Header (GRH)

The GRH is used for routing between subnets. 

When global routing is used on UD QPs, there will be a GRH contained in the first 40 bytes of the receive buffer.

**struct ibv_grh** is used to implement GRHs.


### Protection Domain

A protection domain is used to associate Queue Pairs with Memory Regions and Memory Windows, 
as a means for enabling and controlling network adapter access to Host System memory.

**struct ibv_pd** is used to implement protection domains.

### Asynchronous Events

### Scatter Gather

### Polling

Polling the CQ for completion is getting the details about a WR (Send or Receive) that was posted.

Only after a WR has a completion, the send / receive buffer may be used / reused / freed.

Every WR that does not have a completion (that was polled) is still outstanding.

When a CQE is polled it is removed from the CQ.

Polling is accomplished with the **ibv_poll_cq** operation.

## Typical Application

Two program examples:
  - RDMA_RC_example
  - multicast example
  
The structure of a typical application is as follows:

1. Get the device list; 
    - First you must retrieve the list of available IB devices on the local host.
    - Every device in this list contains both a name and a GUID. For example the device names can be: mthca0, mlx4_1.
2. Open the requested device;
    - Iterate over the device list, choose a device according to its GUID or name and open it.
3. Query the device capabilities;
    - The device capabilities allow the user to understand the supported features (APM, SRQ) and capabilities of the opened device.
4. Allocate a Protection Domain to contain your resources;
    - A Protection Domain (PD) allows the user to restrict which components can interact with only each other. 
    - These components can be AH, QP, MR, MW, and SRQ.
5. Register a memory region;
    - VPI only works with registered memory. 
    - Any memory buffer which is valid in the process’s virtual space can be registered.
    - During the registration process the user sets memory permissions and receives local and 
    remote keys (lkey/rkey) which will later be used to refer to this memory buffer.
6. Create a Completion Queue (CQ);
    - A CQ contains completed work requests (WR).
    - Each WR will generate a completion queue entry (CQE) that is placed on the CQ.
    - The CQE will specify if the WR was completed successfully or not.
7. Create a Queue Pair (QP);
    - Creating a QP will also create an associated send queue and receive queue.
8. Bring up a QP;
    - A created QP still cannot be used until it is transitioned through several states, 
    eventually getting to Ready To Send (RTS).
    - This provides needed information used by the QP to be able send / receive data.
9. Post work requests and poll for completion;
    - Use the created QP for communication operations.
10. Cleanup;
    - Destroy objects in the reverse order you created them:
      - Delete QP
      - Delete CQ
      - Deregister MR
      - Deallocate PD
      - Close device

