# Design Questions

## Rate Limiter and Cache with TTL

Problem 1 (Intern/SDE-1):
"Design a rate limiter that allows 100 requests per user per hour"

Problem 2 (SDE-2):
"Implement a cache with TTL that can handle 10k ops/sec"

### Rate Limiter

Each user has a queue: queue<{user_id, request_timestamp}>

When a request comes in:
- Check the queue, drop requests where timestamp is > 1 hour before
- Count number of remaining requests in the queue
  - if < 100, add the new request to the queue, process the request
  - else, drop the new request

### Cache with TTL

Cache has a queue: queue<{request_id, TTL_timestamp}>

When an operation happens:
- Check the cache
  - Drop entries where TTL expires
  - Count entries from last second (bucket entries per second; use binary search)
    - if more than 10k, return
    - else
      - if find a hit: update the entry with new result and TTL_timestamp, then return the result
      - else: add a new entry with result and TTL_timestamp, then return the result


### Link
- https://medium.com/@kanishks772/i-interviewed-20-engineers-heres-why-most-can-t-code-b647d23f8c89

<br/>

## Zanzibar

Correctness, flexibility, latency, availability, and scalability.

<br/>

## Read-intensive vs Write-intensive Architecture

An architecture is designed as "read-intensive" or "write-intensive" based on whether its primary workload involves more data retrievals (reads) or data modifications (writes)

- The architectural choices for each are fundamentally different, affecting everything from database technology and storage hardware to system design patterns and consistency models.

- <a href="https://www.quora.com/What-are-the-examples-of-read-intensive-or-write-intensive-systems-I-have-been-reading-about-NoSQL-databases-and-came-across-these-two-concepts-I-feel-most-systems-these-days-can-be-read-intensive-and-write">Examples of Read/Write Intensive Systems</a>
  - Read Intensive
    - CDNs
    - Data warehouse
    - Searh engine
    - Social media feed
  - Write Intensive
    - Logging system
    - Real-time analytics
    - Transactional system
    - IoT system
  - Hybrid
    - DBMS
    - NoSQL databases: e.g., MongoDB or Cassandra
    - Web applications


#### Read-intensive architecture

This architecture is optimized for speed and efficiency when retrieving data. The majority of operations are SELECT queries, with data writes being less frequent. 

Use cases:

- Content delivery networks (CDNs): Distributing static content like images and videos to users.
- Web applications: News sites or social media feeds where a small number of writers produce content for millions of readers.
- Caching systems: Caching frequently accessed data to minimize database access.
- Analytics and business intelligence: Reading large datasets to generate reports and insights. 

Key architectural patterns:

- Caching: Implementing layers like a read-through cache to store frequently accessed data closer to the application, reducing database load and latency.
- Database replication: Using read replicas to distribute the read workload across multiple database servers, which scales horizontally.
- Denormalization: Storing data redundantly or in a pre-joined format to allow a single, fast read operation instead of complex, resource-intensive joins.
- Eventual consistency: Because data is read far more often than it is written, a slight delay in data propagation across replicas is often acceptable. Strong, immediate consistency is not a priority.
- Specialized hardware: Using read-intensive SSDs (often TLC or QLC NAND) that offer higher capacity and lower cost, as they are not subject to the same level of wear from frequent write cycles. 

#### Write-intensive architecture

This architecture is built to handle a high volume of data modifications, including inserts, updates, and deletes. Performance is judged by how quickly and reliably the system can persist new information. 
\Use cases:

- Financial trading platforms: Processing a constant stream of new transactions where immediate visibility is critical.
- Video surveillance and logging: Continuously writing new data from multiple sources.
- Online transaction processing (OLTP): E-commerce systems that handle a high volume of small, random updates, such as inventory management.
- Real-time analytics: Ingesting and processing live data streams. 

Key architectural patterns:

- Command Query Responsibility Segregation (CQRS): Separating the data models and stores for reads and writes. This allows the write-side to be highly optimized for insert and update operations without impacting read performance.
- Queueing and batch processing: Using message queues to handle a high volume of writes asynchronously. This allows the system to batch writes and process them more efficiently, rather than processing each one individually.
- Strong consistency: For many write-intensive applications like financial transactions, it is critical that data is consistent across all nodes immediately. The system prioritizes consistency over availability (in the CAP theorem sense).
- Log-structured storage: In key-value stores, writing data to an append-only log allows for very fast sequential writes, which is more efficient than the random writes typical of B-tree-based databases.
- Specialized hardware: Using write-intensive SSDs (often SLC or eMLC NAND) with higher endurance (DWPD - Drive Writes Per Day) to handle the more demanding workload without premature wear. 

#### Architectural trade-offs

| Feature      |	Read-Intensive Architecture	    | Write-Intensive Architecture           |
|--------------|----------------------------------|----------------------------------------|
| Primary Goal | Low latency for data retrieval.	| High throughput for data modification. |
|Consistency Model | Often uses eventual consistency where slight data staleness is acceptable.	| Often requires strong consistency to ensure data integrity. |
|Data Storage  |Uses read-optimized storage (TLC/QLC SSDs) with higher capacity and lower cost per gigabyte. | Uses write-optimized storage (SLC/eMLC SSDs) with high endurance but lower capacity. |
|Scaling Strategy | Scales reads horizontally with replication and caching.	| Often involves vertical scaling (stronger server) or complex partitioning strategies for writes. |
|Design Patterns | Relies on caching, database replicas, and denormalization. | Leverages CQRS, batch processing, and queueing. |

#### CQRS
- https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs
- Command Query Responsibility Segregation (CQRS) is a design pattern that segregates read and write operations for a data store into separate data models. This approach allows each model to be optimized independently and can improve the performance, scalability, and security of an application.
