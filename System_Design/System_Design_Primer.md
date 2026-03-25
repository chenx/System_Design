# System Design Primer

https://github.com/donnemartin/system-design-primer

## How to approach a system design interview question

### Step 1: Outline use cases, constraints, and assumptions

Gather requirements and scope the problem. Ask questions to clarify use cases and constraints. Discuss assumptions.

    Who is going to use it?
    How are they going to use it?
    How many users are there?
    What does the system do?
    What are the inputs and outputs of the system?
    How much data do we expect to handle?
    How many requests per second do we expect?
    What is the expected read to write ratio?

Functional and Non-functional requriements

- Functional: correctness, consistency, features
- Non-functional: scalability, availability, latency, flexibility, maintainability

### Step 2: Create a high level design

Outline a high level design with all important components.

    Sketch the main components and connections
    Justify your ideas

### Step 3: Design core components

Dive into details for each core component. For example, if you were asked to design a url shortening service, discuss:

    Generating and storing a hash of the full url
        MD5 and Base62
        Hash collisions
        SQL or NoSQL
        Database schema
    Translating a hashed url to the full url
        Database lookup
    API and object-oriented design

### Step 4: Scale the design

Identify and address bottlenecks, given the constraints. For example, do you need the following to address scalability issues?

    Load balancer
    Horizontal scaling
    Caching
    Database sharding

### Back-of-the-envelope calculations

You might be asked to do some estimates by hand. Refer to the Appendix for the following resources:

    Use back of the envelope calculations
    Powers of two table
    Latency numbers every programmer should know

### Source(s) and further reading

Check out the following links to get a better idea of what to expect:

    How to ace a systems design interview
    The system design interview
    Intro to Architecture and Systems Design Interviews
    System design template

<br/>

## System Design Topics

### Step 1: Review the scalability video lecture

Scalability Lecture at Harvard

    Topics covered:
        Vertical scaling
        Horizontal scaling
        Caching
        Load balancing
        Database replication
        Database partitioning

### Step 2: Review the scalability article

Scalability

    Topics covered:
        Clones
        Databases
        Caches
        Asynchronism

### High level trade-offs

Everything is a tradeoff:

    Performance vs scalability
    Latency vs throughput
    Availability vs consistency

#### Performance vs scalability

A service is scalable if it results in increased performance in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.1

Another way to look at performance vs scalability:

    If you have a performance problem, your system is slow for a single user.
    If you have a scalability problem, your system is fast for a single user but slow under heavy load.

#### Latency vs throughput

**Latency** is the time to perform some action or to produce some result.

**Throughput** is the number of such actions or results per unit of time.

#### Availability vs consistency

CAP theorem: in a distributed computer system, you can only support two of the following guarantees:

    Consistency - Every read receives the most recent write or an error
    Availability - Every request receives a response, without guarantee that it contains the most recent version of the information
    Partition Tolerance - The system continues to operate despite arbitrary partitioning due to network failures

**CP - consistency and partition tolerance**

Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes.

**AP - availability and partition tolerance**

Responses return the most readily available version of the data available on any node, which might not be the latest. Writes might take some time to propagate when the partition is resolved.

AP is a good choice if the business needs to allow for eventual consistency or when the system needs to continue working despite external errors.

<br/>

### Consistency patterns

**Definition of consistency from the CAP theorem** - Every read receives the most recent write or an error.

#### Weak consistency (Casual Consistency)

After a write, reads may or may not see it. A best effort approach is taken.

This approach is seen in systems such as memcached. Weak consistency works well in real time use cases such as VoIP, video chat, and realtime multiplayer games. For example, if you are on a phone call and lose reception for a few seconds, when you regain connection you do not hear what was spoken during connection loss.

#### Eventual consistency

After a write, reads will eventually see it (typically within milliseconds). Data is replicated asynchronously.

This approach is seen in systems such as DNS and email. Eventual consistency works well in highly available systems.

#### Strong consistency

After a write, reads will see it. Data is replicated synchronously.

This approach is seen in file systems and RDBMSes. Strong consistency works well in systems that need transactions.

<br/>

### Availability patterns

There are two complementary patterns to support high availability: fail-over and replication.

#### Fail-over

**Active-passive**

With active-passive fail-over, heartbeats are sent between the active and the passive server on standby. If the heartbeat is interrupted, the passive server takes over the active's IP address and resumes service.

The length of downtime is determined by whether the passive server is already running in 'hot' standby or whether it needs to start up from 'cold' standby. Only the active server handles traffic.

Active-passive failover can also be referred to as master-slave failover.

**Active-active**

In active-active, both servers are managing traffic, spreading the load between them.

If the servers are public-facing, the DNS would need to know about the public IPs of both servers. If the servers are internal-facing, application logic would need to know about both servers.

Active-active failover can also be referred to as master-master failover.

##### Disadvantage(s): failover

    Fail-over adds more hardware and additional complexity.
    There is a potential for loss of data if the active system fails before any newly written data can be replicated to the passive.


#### Replication

Master-slave and master-master

<br/>

### Domain name system

    NS record (name server) - Specifies the DNS servers for your domain/subdomain.
    MX record (mail exchange) - Specifies the mail servers for accepting messages.
    A record (address) - Points a name to an IP address.
    CNAME (canonical) - Points a name to another name or CNAME (example.com to www.example.com) or to an A record.

### CDN: Content delivery network

A content delivery network (CDN) is a globally distributed network of proxy servers, serving content from locations closer to the user. Generally, static files such as HTML/CSS/JS, photos, and videos are served from CDN, although some CDNs such as Amazon's CloudFront support dynamic content. The site's DNS resolution will tell clients which server to contact.

Serving content from CDNs can significantly improve performance in two ways:

    Users receive content from data centers close to them
    Your servers do not have to serve requests that the CDN fulfills

#### Push CDNs

#### Pull CDNs

#### Disadvantage(s): CDN

    CDN costs could be significant depending on traffic, although this should be weighed with additional costs you would incur not using a CDN.
    Content might be stale if it is updated before the TTL expires it.
    CDNs require changing URLs for static content to point to the CDN.

<br/>

### Load balancer

Load balancers can be implemented with hardware (expensive) or with software such as **HAProxy**.

Layer 4 load balancing: Transport Layer

Layer 7 load balancing: Application Layer

#### Horizontal scaling

Load balancers can also help with horizontal scaling, improving performance and availability. 

**Vertical Scaling**: scaling up a single server on more expensive hardware.

#### Disadvantage(s): horizontal scaling

    Scaling horizontally introduces complexity and involves cloning servers
        Servers should be stateless: they should not contain any user-related data like sessions or profile pictures
        Sessions can be stored in a centralized data store such as a database (SQL, NoSQL) or a persistent cache (Redis, Memcached)
    Downstream servers such as caches and databases need to handle more simultaneous connections as upstream servers scale out

#### Disadvantage(s): load balancer

    The load balancer can become a performance bottleneck if it does not have enough resources or if it is not configured properly.
    Introducing a load balancer to help eliminate a single point of failure results in increased complexity.
    A single load balancer is a single point of failure, configuring multiple load balancers further increases complexity.

<br/>

### Reverse proxy (web server)

A reverse proxy is a web server that centralizes internal services and provides unified interfaces to the public. Requests from clients are forwarded to a server that can fulfill it before the reverse proxy returns the server's response to the client.

#### Load balancer vs reverse proxy

    Deploying a load balancer is useful when you have multiple servers. Often, load balancers route traffic to a set of servers serving the same function.
    Reverse proxies can be useful even with just one web server or application server, opening up the benefits described in the previous section.
    Solutions such as NGINX and HAProxy can support both layer 7 reverse proxying and load balancing.

#### Disadvantage(s): reverse proxy

    Introducing a reverse proxy results in increased complexity.
    A single reverse proxy is a single point of failure, configuring multiple reverse proxies (ie a failover) further increases complexity.

<br/>

### Application layer

#### Microservices

A suite of independently deployable, small, modular services. Each service runs a unique process and communicates through a well-defined, lightweight mechanism to serve a business goal.

#### Service Discovery

#### Disadvantage(s): application layer

    Adding an application layer with loosely coupled services requires a different approach from an architectural, operations, and process viewpoint (vs a monolithic system).
    Microservices can add complexity in terms of deployments and operations.

<br/>

### Database

#### Relational database management system (RDBMS)

A relational database like SQL is a collection of data items organized in tables.

ACID is a set of properties of relational database transactions.

    Atomicity - Each transaction is all or nothing
    Consistency - Any transaction will bring the database from one valid state to another
    Isolation - Executing transactions concurrently has the same results as if the transactions were executed serially
    Durability - Once a transaction has been committed, it will remain so

There are many techniques to scale a relational database: master-slave replication, master-master replication, federation, sharding, denormalization, and SQL tuning.

**Master-slave replication**

**Master-master replication**

Both masters serve reads and writes and coordinate with each other on writes. If either master goes down, the system can continue to operate with both reads and writes.

#### Federation

Federation (or functional partitioning) splits up databases by function. For example, instead of a single, monolithic database, you could have three databases: forums, users, and products, resulting in less read and write traffic to each database and therefore less replication lag. 

#### Sharding

Common ways to shard a table of users is either through the user's last name initial or the user's geographic location.

#### Denormalization

Denormalization attempts to improve read performance at the expense of some write performance. Redundant copies of the data are written in multiple tables to avoid expensive joins. 

Once data becomes distributed with techniques such as federation and sharding, managing joins across data centers further increases complexity. Denormalization might circumvent the need for such complex joins.

In most systems, reads can heavily outnumber writes 100:1 or even 1000:1. A read resulting in a complex database join can be very expensive, spending a significant amount of time on disk operations.

##### Disadvantage(s): denormalization

    Data is duplicated.
    Constraints can help redundant copies of information stay in sync, which increases complexity of the database design.
    A denormalized database under heavy write load might perform worse than its normalized counterpart.

#### SQL tuning

It's important to benchmark and profile to simulate and uncover bottlenecks.

    Benchmark - Simulate high-load situations with tools such as ab.
    Profile - Enable tools such as the slow query log to help track performance issues.

<br/>

### NoSQL

NoSQL is a collection of data items represented in a key-value store, document store, wide column store, or a graph database. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor eventual consistency.

BASE is often used to describe the properties of NoSQL databases. In comparison with the CAP Theorem, BASE chooses availability over consistency.

    Basically available - the system guarantees availability.
    Soft state - the state of the system may change over time, even without input.
    Eventual consistency - the system will become consistent over a period of time, given that the system doesn't receive input during that period.

In addition to choosing between SQL or NoSQL, it is helpful to understand which type of NoSQL database best fits your use case(s).

#### Key-value store

    Abstraction: hash table

A key-value store generally allows for O(1) reads and writes and is often backed by memory or SSD. Data stores can maintain keys in lexicographic order, allowing efficient retrieval of key ranges. Key-value stores can allow for storing of metadata with a value.

#### Document store

    Abstraction: key-value store with documents stored as values

A document store is centered around documents (XML, JSON, binary, etc), where a document stores all information for a given object. Document stores provide APIs or a query language to query based on the internal structure of the document itself.

Some document stores like MongoDB and CouchDB also provide a SQL-like language to perform complex queries. DynamoDB supports both key-values and documents.

#### Wide column store

    Abstraction: nested map ColumnFamily<RowKey, Columns<ColKey, Value, Timestamp>>

A wide column store's basic unit of data is a column (name/value pair). A column can be grouped in column families (analogous to a SQL table). Super column families further group column families. You can access each column independently with a row key, and columns with the same row key form a row. Each value contains a timestamp for versioning and for conflict resolution.

Google introduced Bigtable as the first wide column store, which influenced the open-source HBase often-used in the Hadoop ecosystem, and Cassandra from Facebook. Stores such as BigTable, HBase, and Cassandra maintain keys in lexicographic order, allowing efficient retrieval of selective key ranges.

Wide column stores offer high availability and high scalability. They are often used for very large data sets.

#### Graph database

    Abstraction: graph

In a graph database, each node is a record and each arc is a relationship between two nodes. Graph databases are optimized to represent complex relationships with many foreign keys or many-to-many relationships.

Graphs databases offer high performance for data models with complex relationships, such as a social network. They are relatively new and are not yet widely-used; it might be more difficult to find development tools and resources. Many graphs can only be accessed with REST APIs.

<br/>

### SQL or NoSQL

Reasons for SQL:

    Structured data
    Strict schema
    Relational data
    Need for complex joins
    Transactions
    Clear patterns for scaling
    More established: developers, community, code, tools, etc
    Lookups by index are very fast

Reasons for NoSQL:

    Semi-structured data
    Dynamic or flexible schema
    Non-relational data
    No need for complex joins
    Store many TB (or PB) of data
    Very data intensive workload
    Very high throughput for IOPS

Sample data well-suited for NoSQL:

    Rapid ingest of clickstream and log data
    Leaderboard or scoring data
    Temporary data, such as a shopping cart
    Frequently accessed ('hot') tables
    Metadata/lookup tables

<br/>

### Cache

#### Client caching

Caches can be located on the client side (OS or browser), server side, or in a distinct cache layer.

#### CDN caching

CDNs are considered a type of cache.

#### Web server caching

Reverse proxies and caches such as Varnish can serve static and dynamic content directly. Web servers can also cache requests, returning responses without having to contact application servers.

#### Database caching

Your database usually includes some level of caching in a default configuration, optimized for a generic use case. Tweaking these settings for specific usage patterns can further boost performance.

#### Application caching

In-memory caches such as Memcached and Redis are key-value stores between your application and your data storage.

#### Caching at the database query level

Whenever you query the database, hash the query as a key and store the result to the cache. This approach suffers from expiration issues.

#### Caching at the object level

See your data as an object, similar to what you do with your application code. Have your application assemble the dataset from the database into a class instance or a data structure(s):

    Remove the object from cache if its underlying data has changed
    Allows for asynchronous processing: workers assemble objects by consuming the latest cached object

Suggestions of what to cache:

    User sessions
    Fully rendered web pages
    Activity streams
    User graph data

#### When to update the cache

##### Cache-aside

The application is responsible for reading and writing from storage. The cache does not interact with storage directly. 

##### Write-through

Write-through is a slow overall operation due to the write operation, but subsequent reads of just written data are fast.

##### Write-behind (write-back)

In write-behind, the application does the following:

    Add/update entry in cache
    Asynchronously write entry to the data store, improving write performance

Disadvantage(s): write-behind

    There could be data loss if the cache goes down prior to its contents hitting the data store.
    It is more complex to implement write-behind than it is to implement cache-aside or write-through.

##### Refresh-ahead

You can configure the cache to automatically refresh any recently accessed cache entry prior to its expiration.

#### Disadvantage(s): cache

    Need to maintain consistency between caches and the source of truth such as the database through cache invalidation.
    Cache invalidation is a difficult problem, there is additional complexity associated with when to update the cache.
    Need to make application changes such as adding Redis or memcached.

<br/>

### Asynchronism

#### Message queues

Message queues receive, hold, and deliver messages. If an operation is too slow to perform inline, you can use a message queue with the following workflow:

    An application publishes a job to the queue, then notifies the user of job status
    A worker picks up the job from the queue, processes it, then signals the job is complete

The user is not blocked and the job is processed in the background. During this time, the client might optionally do a small amount of processing to make it seem like the task has completed. For example, if posting a tweet, the tweet could be instantly posted to your timeline, but it could take some time before your tweet is actually delivered to all of your followers.

Redis is useful as a simple message broker but messages can be lost.

RabbitMQ is popular but requires you to adapt to the 'AMQP' protocol and manage your own nodes.

Amazon SQS is hosted but can have high latency and has the possibility of messages being delivered twice.

#### Task queues

Tasks queues receive tasks and their related data, runs them, then delivers their results. 

#### Back pressure

If queues start to grow significantly, the queue size can become larger than memory, resulting in cache misses, disk reads, and even slower performance. Back pressure can help by limiting the queue size, thereby maintaining a high throughput rate and good response times for jobs already in the queue. 

#### Disadvantage(s): asynchronism

    Use cases such as inexpensive calculations and realtime workflows might be better suited for synchronous operations, as introducing queues can add delays and complexity.

<br/>

### Communication

#### Hypertext transfer protocol (HTTP)

HTTP is a method for encoding and transporting data between a client and a server. It is a request/response protocol: clients issue requests and servers issue responses with relevant content and completion status info about the request. HTTP is self-contained, allowing requests and responses to flow through many intermediate routers and servers that perform load balancing, caching, encryption, and compression.

#### Transmission control protocol (TCP)

TCP is a connection-oriented protocol over an IP network. Connection is established and terminated using a handshake. All packets sent are guaranteed to reach the destination in the original order and without corruption through:

    Sequence numbers and checksum fields for each packet
    Acknowledgement packets and automatic retransmission

#### User datagram protocol (UDP)

UDP is connectionless. Datagrams (analogous to packets) are guaranteed only at the datagram level. Datagrams might reach their destination out of order or not at all. UDP does not support congestion control. Without the guarantees that TCP support, UDP is generally more efficient.

Use UDP over TCP when:

    You need the lowest latency
    Late data is worse than loss of data
    You want to implement your own error correction

#### Remote procedure call (RPC)

In an RPC, a client causes a procedure to execute on a different address space, usually a remote server. The procedure is coded as if it were a local procedure call, abstracting away the details of how to communicate with the server from the client program. Remote calls are usually slower and less reliable than local calls so it is helpful to distinguish RPC calls from local calls. Popular RPC frameworks include Protobuf, Thrift, and Avro.

RPC is a request-response protocol:

    Client program - Calls the client stub procedure. The parameters are pushed onto the stack like a local procedure call.
    Client stub procedure - Marshals (packs) procedure id and arguments into a request message.
    Client communication module - OS sends the message from the client to the server.
    Server communication module - OS passes the incoming packets to the server stub procedure.
    Server stub procedure - Unmarshalls the results, calls the server procedure matching the procedure id and passes the given arguments.
    The server response repeats the steps above in reverse order.

HTTP APIs following REST tend to be used more often for public APIs.
Disadvantage(s): RPC

    RPC clients become tightly coupled to the service implementation.
    A new API must be defined for every new operation or use case.
    It can be difficult to debug RPC.
    You might not be able to leverage existing technologies out of the box. For example, it might require additional effort to ensure RPC calls are properly cached on caching servers such as Squid.

#### Representational state transfer (REST)

REST is an architectural style enforcing a client/server model where the client acts on a set of resources managed by the server. The server provides a representation of resources and actions that can either manipulate or get a new representation of resources. All communication must be stateless and cacheable.

There are four qualities of a RESTful interface:

    Identify resources (URI in HTTP) - use the same URI regardless of any operation.
    Change with representations (Verbs in HTTP) - use verbs, headers, and body.
    Self-descriptive error message (status response in HTTP) - Use status codes, don't reinvent the wheel.
    HATEOAS (HTML interface for HTTP) - your web service should be fully accessible in a browser.

Sample REST calls:
```
GET /someresources/anId

PUT /someresources/anId
{"anotherdata": "another value"}
```

REST is focused on exposing data. It minimizes the coupling between client/server and is often used for public HTTP APIs. REST uses a more generic and uniform method of exposing resources through URIs, representation through headers, and actions through verbs such as GET, POST, PUT, DELETE, and PATCH. Being stateless, REST is great for horizontal scaling and partitioning.

Disadvantage(s): REST

- With REST being focused on exposing data, it might not be a good fit if resources are not naturally organized or accessed in a simple hierarchy. For example, returning all updated records from the past hour matching a particular set of events is not easily expressed as a path. With REST, it is likely to be implemented with a combination of URI path, query parameters, and possibly the request body.
- REST typically relies on a few verbs (GET, POST, PUT, DELETE, and PATCH) which sometimes doesn't fit your use case. For example, moving expired documents to the archive folder might not cleanly fit within these verbs.
- Fetching complicated resources with nested hierarchies requires multiple round trips between the client and server to render single views, e.g. fetching content of a blog entry and the comments on that entry. For mobile applications operating in variable network conditions, these multiple roundtrips are highly undesirable.
- Over time, more fields might be added to an API response and older clients will receive all new data fields, even those that they do not need, as a result, it bloats the payload size and leads to larger latencies.


#### Security

    Encrypt in transit and at rest.
    Sanitize all user inputs or any input parameters exposed to user to prevent XSS and SQL injection.
    Use parameterized queries to prevent SQL injection.
    Use the principle of least privilege.
