# Cache System

11/10 System Design. Apple.

Design a global distributed caching system.
1. Globally distributed.
2. Serving and Maintenance should be as cheap as possible.
3. Bursty look ups, which could result in one million key value pair look up per sec, per region/location/data center.
   The system should be able to scale up or down quickly to address the bursty QPS.
4. Assume each key value: 10K bytes in value, serialized.
5. Assume each data center/ edge needs to hold 10TB.
6. Read after write consistency.
7. Keep up runtime availability to 99.999% . 5.26 minutes / year


Design

In-house search engine
- latency: a few milliseconds: 5-10
  - SSD: expensive
  - distributed servers: 
    - CDN (optional)
    - only use 10 data centers.
      - distribute according to number of users with physical adjacency
      - data center 
- in house 
  - cache not needed
  - just search engine fetch and serve

Requirement
- functional:
  - global
  - cost low

- non-functional
  - scalable
  - consistency
  - runtime availability to 99.999% . 5.26 minutes / year

- additional
  - security
  - monitoring / alerting

- assumption, estimations, calculations
  - QPS: millions / sec, per region/location/data center
  - k/v pair: 10k bytes
  - each data center: 10 TB.

  - CPU - not a bottleneck
  - RAM
    - single machine cache
      - compression
      - save important kv data in memory only, use LRU / LFU
      - horizontal scale to many servers / data centers (to be discussed)
      - SSD, 
      - access speed. 
    - 128 GB mem is common

  - Disk
    - 10 TB
      - If store data in memory, with 128 GB / machine, needs 100 machines for 10 TB.
      - Or leave data on HDD, read 1MB data sequentially from HDD needs 30 ms. Ok for in-house search engine.
      - 10 machines per data center.
    - 1 PB =~ 100 machine.
  - Network bandwidth
    - Read: 10k * 10^6 = 10 GB / sec.
    - Write: relatively low: 10K * 10^4 = 100MB / sec

High level Design
- workflow  
```
                                                      N1     N2
                                               N0     |     /     N3
  client <--> load balancer <--> coordinator —-/------|--- /          N4
                                               N9                 N5
                                                    N8   N7   N6
```

  - choose the 3 servers mapped by a key:
    - find the first server mapped by the key, this is the first server to use
    - go clockwise along the ring, use the next 2 servers
  - read:
    - use the latest
    - \[server, version] for each record (k/v pair)
      - version can be timestamp
    - compare the version, use the most recent version.
      - if there is a conflict, then the client needs to resolve it.
    - Read 1 MB sequentially from HDD : 30ms, acceptable for in-house search engine.
      - For user perception, 300 ms latency should be fine, this is 10x of 30ms.

  - write:
    - 1% of read.
    - Quorum consensus:
      - get response for each write to be considered successful, from W servers.
      - attach [server, version], where version is timestamp.
      - async write is enough.
        - for read latency, write to servers in same data center
        - Write: relatively low: 10K * 10^4 = 100MB / sec
        - for additional data safety, make extra copies to different data centers / continents.
          - offline.
          - depends on resource availability
    - for high write load: Async write: Message queue, write in batch.

- Data model
- API

Let's assume Google Cloud uptime: 
- 99.5%. (one instance uptime guarantee). ~ 9 hours / year
- (1 - 99.5%) ^ n <= 1 - 99.999%, solve n for num of servers.
  0.005^n <= 0.00001, n = 3
  - Note: with 2 servers, (1-99.5%)^2 = 99.9975%, or ~13 minutes of downtime / year.
  - 99.999% is 5.26 minutes of downtime / year.

Deep dive
- core components
- scaling 
  - partitioning:
    - traditional way: hash(key) % N, N is number of servers. add/remove cause most keys change
    - consistent hash:
      - distribute keys (SHA-1, 160bits, 2^160) and servers using the same hash function, into a hash-ring
        hash(key) % hash_space, 
      - lookUP: look clockwise from the key to the next server
      - virtual nodes: to distribute evenly
    - add / remove servers without causing much key re-distribution
  - replication:
    - at least 3 copies
    - 3 servers from where the first server where the key belongs.
  - consistency
    - Read/Write: N, R, W
      - write: get response from W servers 
      - read: get from R servers
      - tunable:
        - R = 1, W > 1: fast read
        - cassandra
        - whoever comes latest is the winner
  - solution for inconsistency
    - read from multiple servers (R)
      - whoever comes latest is the winner
      - versioning (in case read from multiple servers)
    - quorum consensus
  - handle failures
    - detection of failed server
      - gossip protocol: each server keeps a list of all nodes, send a heartbeat message to other servers
      - if not receive a heartbeat after some time (by multiple server), then consider the server down.
    - temporary failure of a server
      - ignore the failed server (sloppy quorum)
      - use another server (hindered handoff) when the server is down, recover after the server is back online.
    - permanent server down. 
      - replace the server
    - distribute in multiple data centers at different geographic locations (>= 3)
