# Key-Value Store

## Requirements

Functional
- The size of a key-value pair is small: less than 10 KB.
- Ability to store big data.
- Automatic scaling: The addition/deletion of servers should be automatic based on traffic.

Non-functional
- High availability: The system responds quickly, even during failures.
- High scalability: The system can be scaled to support large data set.
- Tunable consistency.
- Low latency.

Additional
- Monitoring and alerting

## High level design

### Single server key-value store

- In memory storage (as hash table)
- Cons: size limit
- Mitigation
  - data compression
  - store only frequently used data in memory

### Distributed key-value store

- CAP theorem
  - CP (consistency and partition tolerance) systems: a CP key-value store supports consistency and partition tolerance while sacrificing availability.
  - AP (availability and partition tolerance) systems: an AP key-value store supports availability and partition tolerance while sacrificing consistency.
  - CA (consistency and availability) systems: a CA key-value store supports consistency and availability while sacrificing partition tolerance. 
    Since network failure is unavoidable, a distributed system must tolerate network partition. Thus, a CA system cannot exist in realworld applications.

System components
- Data partition
  - Consistent hashing - hash ring - virtual nodes
  - 2 tasks
    - distribute data evenly on different servers
    - minimize data movement when adding/removing a server
  - Heterogeneity: the number of virtual nodes for a server is proportional to the server capacity
- Data replication
  - after a key is mapped to a position on the hash ring, walk clockwise from that position and choose the first N servers on the ring to store data copies (e.g., N=3)
  - use multiple data centers
- Consistency
  - Quorum consensus can guarantee consistency for both read and write operations: N, W, R
  - N: number of replicas
  - W: A write quorum of size W. For a write operation to be considered as successful, write operation must be acknowledged from W replicas.
  - R: A read quorum of size R. For a read operation to be considered as successful, read operation must wait for responses from at least R replicas.
  - Depending on the requirement, we can tune the values of W, R, N to achieve the desired level of consistency.
    - W=1, R=N: fast write
    - R=1, W=N: fast read
    - R+W > N: strong consistency (often N=3, R=W=2), since there is at least one overlapping node hwith the lastest data
    - R+W < N: strong consistency not guaranteed
    
- Inconsistency resolution
  - Versioning and vector clocks are used to solve inconsistency problems
  - A vector clock is a [server, version] pair associated with a data item. It can be used to check if one version precedes, succeeds, or in conflict with others.
    - Confict will be resolved by client
    - Down side of vector clock: 1) complexity, 2) version can go rapidly (solution: remove old versions after a threshold)
- Handling failures
  - Failure detection
    - requires at least two independent sources of information to mark a server down.
    - use decentralized failure detection methods like gossip protocol
      - each node keeps a list of member nodes
      - each node periodically sends heartbeats to other nodes, and update member list
      - if heartbeat does not increase after a threshold, the node is considered dead
  - Handle temporary failure
    - In case of strong quorum, read/write will be blocked
    - ways to increase availability:
      - sloppy quorum: use the first R/W nodes for read/write, the dead node is ignored
      - hinted handoff: choose the next node to handle read/write temporarily
  - Handle permanent failure
    - anti-entropy protocal to keep replicas in sync
      - Anti-entropy involves comparing each piece of data on replicas and updating each replica to the newest version.
    - Merkle tree (hash tree)
      - A Merkle tree is used for inconsistency detection and minimizing the amount of data transferred.
      - Quoted from Wikipedia: “A hash tree or Merkle tree is a tree in which every non-leaf node is labeled with the hash of the labels or values (in case of leaves) of its child nodes.
        Hash trees allow efficient and secure verification of the contents of large data structures”.
  - Handle data center outage
    - replicate data to multiple data centers
- System architecture diagram
  - page 104, 105
- Write path
  - commit log; memory; flush out to SSTable on Disk
- Read path
  - If key is in memory, return directly; otherwise
  - Use bloom filter to find out which server SST (Sorted String Table) contains the key, read it into memory and return


### Workflow

```
                                 n0
        read/write         n7     |    / n1
client ------------> n6 (coordinator)    -    n2
        response           n5            n3
                                 n4
```

### Data model

### API

- put(key, value)
- get(key)

## Deep dive

## Scaling

## Summary

Page 108.

- Ability to store big data: use consistent hashing to spread the load across servers
- High availability read: data replication, multi-data center setup
- High availability write: versioning and conflict resolution by vector clicks
- Dataset partition: consistent hashing
- Incremental scalability: consistent hashing
- Heterogeneity: consistent hashing
- Tunable consistency: Quorum consensus
- Handling temporary failures: sloppy quorum and hinted handoff
- Handling permanent failure: merkle tree
- Handling data center failure: cross-data center replication

 
