1. Learn the basics of vertical vs. horizontal scalability, what they mean and when each applies.

2. Understand latency (response time) and throughput (requests/sec) as key system metrics.

3. Practice quick, back-of-the-envelope capacity estimations, how much traffic, storage, or compute you need.

4. Nail down networking fundamentals (DNS, TCP/IP, load balancers, firewalls).

5. Understand the difference between SQL and NoSQL databases, why and when to use each.

6. Learn basic data modeling, how to design tables, relationships, and schemas.

7. Study how indexing works and why it’s critical for performance.

8. Know the pros and cons of normalization vs. denormalization in database design.
   - Normalization organizes data into separate tables to reduce redundancy and improve integrity, while denormalization combines data into fewer, broader tables to improve read performance by reducing the need for joins.
   - Normalization is ideal for write-heavy or transactional systems (\(OLTP\)) where consistency and data integrity are paramount, whereas denormalization is beneficial for read-intensive applications (\(OLAP\)) or data warehouses where fast data retrieval is critical.
   - The choice between the two involves a trade-off between performance, storage, and data integrity

9. Master the concept of caching, where to cache (client, server, DB), and why it speeds things up.
   - Redis is more powerful, more popular, and better supported than memcached.
   - Memcached can only do a small fraction of the things Redis can do.

10. Dive into cache invalidation, when and how cached data should be refreshed.

11. Learn load balancing techniques, round robin, least connections, weighted, etc.

12. Get comfortable with CDNs and edge caching for global content delivery.
    - Origin server: The original source of website or application content.
    - Edge servers: Servers within the CDN's network, located in various geographic regions.

13. Explore sharding/partitioning, splitting databases to scale horizontally.

14. Learn how replication works (master-slave, master-master) and why it helps with availability.

15. Understand different consistency models (strong, eventual, causal, etc.).

16. Deep-dive into the CAP theorem and its trade-offs.

17. Study concurrency control, locks, transactions, isolation levels.

18. Learn basics of multithreading and parallelism in backend code.

21. Understand idempotency in APIs, making repeated calls safe.

22. Practice implementing rate limiting and throttling to protect services.
    - https://www.geeksforgeeks.org/system-design/api-throttling-vs-api-rate-limiting-system-design/
    - Rate limiting: Blocks requests entirely once the limit is reached.
    - Throttling: Slows down requests after a certain threshold.

23. Explore asynchronous processing, why queues and streams matter for decoupling.

24. Learn about message brokers (RabbitMQ, Kafka, SQS, etc.).

25. Get a grip on backpressure, how systems signal when they’re overloaded.

26. Understand delivery semantics, at most once, at least once, exactly once.

27. Study RESTful API design, good URIs, methods, status codes.

    - GET: Retrieve a resource or a collection of resources.
    - POST: Create a new resource.
    - PUT: Update an existing resource (replace the entire resource).
    - PATCH: Partially update an existing resource.
    - DELETE: Remove a resource.

28. Learn about RPC/gRPC and when it’s better than REST.

29. Practice API versioning, how to change APIs safely over time.

30. Understand OpenAPI/Swagger and API documentation best practices.

31. Get clear on authentication (AuthN) and authorization (AuthZ), OAuth, JWT, API keys.

32. Learn about circuit breakers, timeouts, and retries for resilient systems.

33. Explore bulkheads and how they isolate failures in microservices.

34. Understand fail-fast vs. graceful degradation strategies.

35. Set up observability basics, logging, monitoring, tracing.

36. Learn about health checks and heartbeats for service uptime.

37. Use dashboards and alerts to monitor system health.

38. Practice setting up distributed tracing to follow requests end-to-end.

39. Learn how to build for redundancy and design failover systems.

40. Use service discovery (Consul, etcd, Eureka) for dynamic systems.

41. Master safe deployment strategies, blue-green, canary, rolling updates.

42. Understand the difference between monoliths and microservices, and why both matter.
