# Consistent Hashing

## Solution from "System Design Interview" by Alex Xu

The rehashing problem
- serverIndex = hash(key) % N, where N is the size of the server pool
- add/remove server causes problem: redistribution of the keys

### Consistent hashing

Quoted from Wikipedia: "Consistent hashing is a special kind of hashing such that when a
hash table is re-sized and consistent hashing is used, only **k/n** keys need to be remapped on
average, where k is the number of keys, and n is the number of slots. In contrast, in most
traditional hash tables, a change in the number of array slots causes nearly all keys to be
remapped.”

### Hash space and hash ring

SHA-1’s hash space goes from 0 to 2^160 - 1

### Hash servers

Using the same hash function f, we map servers based on server IP or name onto the ring.

To determine which server a key is stored on, we go clockwise from the key position on the
ring until a server is found.

### Add a server

Using the logic described above, adding a new server will only require redistribution of a
fraction of keys.

- Before server 4 is added, key0 is stored on server 0.
- Now, key0 will be stored on server 4 because server 4 is the first server it encounters by going clockwise from key0’s position on the ring.
- The other keys are not redistributed based on consistent hashing algorithm.

### Remove a server

When a server is removed, only a small fraction of keys require redistribution with consistent hashing.

### Two issues in the basic approach

The consistent hashing algorithm was introduced by Karger et al. at MIT [1]. The basic steps are:
- Map servers and keys on to the ring using a uniformly distributed hash function.
- To find out which server a key is mapped to, go clockwise from the key position until the first server on the ring is found.

Two problems are identified with this approach. 
- First, it is impossible to keep the same size of partitions on the ring for all servers considering a server can be added or removed.
- Second, it is possible to have a non-uniform key distribution on the ring.

A technique called virtual nodes or replicas is used to solve these problems.

#### Virtual nodes 

A virtual node refers to the real node, and each server is represented by multiple virtual nodes on the ring.

With virtual nodes, each server is responsible for multiple partitions.

s1_0 - s0_0 - s1_1 - s0_1 - s1_2 - s0_2

As the number of virtual nodes increases, the distribution of keys becomes more balanced.
- This is because the standard deviation gets smaller with more virtual nodes, leading to balanced data distribution.
- The standard deviation will be smaller when we increase the number of virtual nodes. However, more spaces are needed to store data about virtual nodes.
- This is a tradeoff, and we can tune the number of virtual nodes to fit our system requirements.

### Find affected keys

When a server is added or removed, a fraction of data needs to be redistributed. How can we
find the affected range to redistribute the keys?

In Figure 5-14, server 4 is added onto the ring. The affected range starts from s4 (newly
added node) and moves anticlockwise around the ring until a server is found (s3). Thus, keys
located between s3 and s4 need to be redistributed to s4.

When a server (s1) is removed as shown in Figure 5-15, the affected range starts from s1
(removed node) and moves anticlockwise around the ring until a server is found (s0). Thus,
keys located between s0 and s1 must be redistributed to s2.

### Summary

key points:
- problem of traditional method: add/remove server
- SHA-1 hash space / ring
- calculate hash value for server and data using the same hash function; find key's server by going clockwise
- add a server / remove a server: only k/n keys need move
- problem: uneven distribution of partitions and keys
  - solution: virtual nodes

The benefits of consistent hashing include:
- Minimized keys are redistributed when servers are added or removed.
- It is easy to scale horizontally because data are more evenly distributed.
- Mitigate hotspot key problem.

Consistent hashing is widely used in real-world systems, including some notable ones:
- Partitioning component of Amazon’s Dynamo database [3]
- Data partitioning across the cluster in Apache Cassandra [4]
- Discord chat application [5]
- Akamai content delivery network [6]
- Maglev network load balancer [7]


<br />

## Another solution

https://www.geeksforgeeks.org/system-design/consistent-hashing/

### What is Constent Hashing

- Consistent hashing is a distributed hashing technique used in load balancing. 
- The goal is to minimize the need for rehashing when the number of nodes in a system changes. 
  - Decide which request will be handled by which server

### Issues of traditional hashing methods

- Uneven Distribution of Data: Traditional hashing methods often lead to an uneven distribution of data across servers. When you use a simple hash function, some servers may get more data, while others get very little.
- Scalability Problems: A sizable amount of the data must be redistributed among all servers in classic hashing whenever a server (node) is added or removed. As a result, practically all data must be rehashed and reassigned, which is ineffective and results in delays or downtime.
- Inflexibility with Changing Number of Servers: If your application requires scaling up by adding more servers or scaling down by removing some, traditional hashing methods struggle to adapt. The entire system becomes unstable, and large amounts of data need to be moved.
- Node Failure Handling: When a server fails in a traditional hashing setup, all the data on that server becomes inaccessible until the server is back up or the data is redistributed. There is no good way to handle node failures.
- Overhead of Rehashing: When the system grows or shrinks, traditional hashing requires rehashing of most keys to different servers, which causes a high amount of overhead.

### Phases of Consistent Hashing

- Phase 1: Hash Function Selection: Selecting the hash algorithm to link keys to network nodes is the first stage in consistent hashing. This hash function should be deterministic and produce a different value for each key. The selected hash function will be used to map keys to nodes in a consistent and predictable manner.
- Phase 2: Node Assignment: Based on the hash function's findings, nodes in the network are given keys in this phase. The nodes are organized in a circle, and the keys are given to the node that is situated closest to the key's hash value in a clockwise direction in the circle.
- Phase 3: Key Replication: It's critical to make sure that data is accessible in a distributed system even in the case of node failures. Keys can be copied across a number of network nodes to accomplish this. In the event that one node fails, this helps to guarantee that data is always accessible.
- Phase 4: Node Addition/Removal: It can be required to remap the keys to new nodes in order to maintain system balance when nodes are added to or deleted from the network. By only remapping just a small number of keys to the new node, consistent hashing minimizes the impact of added or deleted nodes.
- Phase 5: Load balancing: Consistent hashing helps in distributing the load among the network's nodes. To keep the system balanced and effective when a node is overloaded, portions of its keys can be remapped to other nodes.
- Phase 6: Failure Recovery: If a node fails, the keys that are assigned to it can be remapped to other nodes in the network. This enables data to remain accurate and always available, even in the case of a node failure.

### Implementation of Consistent Hashing algorithm

- Step 1: Choose a Hash Function:
  - Select a hash function that produces a uniformly distributed range of hash values. Common choices include MD5, SHA-1, or SHA-256.
- Step 2: Define the Hash Ring:
  - Represent the range of hash values as a ring. This ring should cover the entire possible range of hash values and be evenly distributed.
- Step 3: Assign Nodes to the Ring:
  - Assign each node in the system a position on the hash ring. This is typically done by hashing the node's identifier using the chosen hash function.
- Step 4: Key Mapping:
  - When a key needs to be stored or retrieved, hash the key using the chosen hash function to obtain a hash value.
  - Find the position on the hash ring where the hash value falls.
  - Walk clockwise on the ring to find the first node encountered. This node becomes the owner of the key.
- Step 5: Node Additions:
  - When a new node is added, compute its position on the hash ring using the hash function.
  - Identify the range of keys that will be owned by the new node. This typically involves finding the predecessor node on the ring.
  - Update the ring to include the new node and remap the affected keys to the new node.
- Step 6: Node Removals:
  - When a node is removed, identify its position on the hash ring.
  - Identify the range of keys that will be affected by the removal. This typically involves finding the successor node on the ring.
  - Update the ring to exclude the removed node and remap the affected keys to the successor node.
- Step 7: Load Balancing:
  - Periodically check the load on each node by monitoring the number of keys it owns.
  - If there is an imbalance, consider redistributing some keys to achieve a more even distribution.

