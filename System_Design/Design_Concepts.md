# Design Concepts

- [Design Concepts](#design-concepts)
  * [Logging best practices](#logging-best-practices)
  * [What happens when typing a URL in web browser](#what-happens-when-typing-a-url-in-web-browser)
  * [How does HTTPS work](#how-does-https-work)
  * [Match 7-layer OSI model to 4-layer TCP/IP model](#match-7-layer-osi-model-to-4-layer-tcpip-model)
  * [HTTP vs RPC](#http-vs-rpc)
  * [RPC vs gRPC](#rpc-vs-grpc)
  * [Object store](#object-store)

## Logging best practices

- https://www.honeycomb.io/blog/engineers-checklist-logging-best-practices
- https://betterstack.com/community/guides/logging/logging-best-practices/
- https://sematext.com/blog/best-practices-for-efficient-log-management-and-monitoring/

Best practices

- structured data
  - plain text, JSON
  - Standard field names and types: Time, level, msg, user_id, session_id
  - Levels: INFO, WARNING, ERROR, FATAL, DEBUG/TRACE
  - Unique ID
- Avoid PII
- Use a centralized logging management system
- Configure retention policy
- Protect logs with access control
- Processing:
  - Setup alerts
  - Aggregate and centralize your logs


## What happens when typing a URL in web browser

https://y03l2iufsbl.feishu.cn/docx/Dvtbd8wBwokz7jxqkOEcnsUDndg

- DNS lookup (13 root servers)
- establish TCP connection (TCP packet: port, IP packet: ip address)
- Establish secure communication channel if HTTPS is used
  - client sends request to server
  - server responds with SSL certificate and RSA public key
  - client verifies SSL certificate at a CA (Certificate Authority), generates a session key, encrypts it using the server public key, and sends it back to server
  - server decrypts the session key using its RSA private key
  - client and server communicates using the session key for encryption and decryption from now on
- send HTTP request
  - HTTP vs HTTPS (SSL certificate, RSA public/private key, session key, CA, encryption/decryption)
  - GET vs POST (format, size, side effect, security, browser cache/bookmark)
    - https://www.w3schools.com/tags/ref_httpmethods.asp
    - https://blog.postman.com/get-vs-post/
  - IP packet (src/dst IP address) and TCP packet (src/dst port, seq number, congestion control)
- web server handles request and send response back
- web browser parsing (DOM, CSSOM) and render
- finish connection

### Computer Network basics

https://www.geeksforgeeks.org/computer-networks/basics-computer-networking/

## How does HTTPS work

Key Components and Process:

- TLS/SSL Certificate: A digital document provided by a Certificate Authority (CA) that authenticates a website's identity.
  - https://www.ssl.com/article/what-is-a-certificate-authority-ca/
- Encryption: Data is scrambled so only the intended recipient can read it.
- Port 443: HTTPS operates over port 443, unlike unsecure HTTP which uses port 80. 

The HTTPS Handshake (How it works): 

- Client Hello: The browser connects to the server and requests a secure session.
- Certificate Exchange: The server sends its SSL certificate and public key to the browser.
- Authentication: The browser verifies the certificate with a trusted CA.
- Key Exchange: The browser generates a session key, encrypts it with the server's public key, and sends it back.
- Secure Connection: The server decrypts the session key using its private key, and both parties now use this key to encrypt all data for the session. 

Benefits of HTTPS:

- Confidentiality: Encrypts data, making it unreadable to attackers.
- Integrity: Ensures data cannot be modified or corrupted during transfer.
- Authentication: Proves you are communicating with the intended website.
- Trust: Browsers mark HTTP sites as "not secure," while HTTPS boosts user trust. 

## Match 7-layer OSI model to 4-layer TCP/IP model

- https://resources.l-p.com/knowledge-center/osi-vs-tcp-ip-key-differences-between-network-models
- https://diog0net0sec.medium.com/understanding-the-osi-model-and-tcp-ip-369ca2978ab9
- https://medium.com/@sesmiat/understanding-the-journey-of-http-requests-over-tcp-connections-39f32b1ca10d

The OSI 7-layer model maps to the 4-layer TCP/IP model by 
- combining the top three OSI layers (Application, Presentation, Session) into one Application layer,
- and the bottom two (Data Link, Physical) into a Network Access layer.
- Transport and Network layers remain largely equivalent in both models.

Key Differences: 
- OSI (Open Systems Interconnection): A 7-layer theoretical, conceptual framework used for teaching.
- TCP/IP (Transmission Control Protocol/Internet Protocol): A 4-layer functional, practical model used for actual network implementation.

## HTTP vs RPC

HTTP and RPC are distinct approaches to inter-process communication, particularly in distributed systems.

### HTTP (Hypertext Transfer Protocol)

- HTTP is a protocol primarily designed for transferring hypermedia documents like HTML, images, and other resources over the internet.
- It operates on a request-response model, where clients send requests to servers, and servers respond with the requested resources or status codes.
- HTTP is stateless, meaning each request is independent of previous requests, although session management can be implemented on top of it.
- RESTful APIs, a common architectural style, leverage HTTP methods (GET, POST, PUT, DELETE) to perform operations on resources identified by URLs.
- HTTP typically uses text-based data formats like JSON or XML for message bodies. 

### RPC (Remote Procedure Call)

- RPC is a protocol that allows a program to execute a function or procedure in a different address space (e.g., on a remote server) as if it were a local call. 
- The focus is on invoking actions or operations rather than manipulating resources. 
- RPC frameworks often use an Interface Definition Language (IDL) to define the remote procedures and their parameters, enabling communication between different programming languages and operating systems.
- Examples include gRPC (which uses Protocol Buffers for efficient serialization and HTTP/2 for transport) and traditional RPC using TCP/UDP.
- RPC can be either stateful or stateless, depending on the implementation. 

### Key Differences:

- Focus:
    HTTP, especially in a RESTful context, focuses on resources and standard operations (CRUD). RPC focuses on executing specific functions or procedures.

- Communication Model:
    HTTP uses a resource-based model. RPC uses a function-based model, mimicking local function calls.

- Transport Protocol:
    HTTP primarily uses HTTP/HTTPS. RPC can use various transport protocols, including HTTP, TCP, or lower-level protocols. 

- Serialization:
    HTTP commonly uses JSON or XML. RPC frameworks like gRPC often use more efficient binary serialization formats like Protocol Buffers.

- Performance:
    High-performance RPC implementations like gRPC can offer significant performance advantages due to efficient serialization and transport protocols. 

### When to use each:

- HTTP/REST: 
Well-suited for web services, public APIs, and scenarios involving CRUD operations on resources, especially when leveraging web infrastructure for caching and security.

- RPC:
Preferred for high-performance, low-latency communication, internal microservices communication, real-time applications, and scenarios where complex operations resembling function calls are central.

### Links

- https://charleswan111.medium.com/rpc-vs-http-a-deep-dive-into-communication-models-in-distributed-systems-7c8927586935

<br/>


## RPC vs gRPC

**RPC (Remote Procedure Call)** is a general concept that allows a program to cause a procedure (or function) to execute in another address space (typically on a remote computer) as if it were a local procedure call. It's a broad category of protocols and frameworks that enable inter-process communication in distributed systems.

**gRPC (Google Remote Procedure Call)** is a specific, modern, high-performance RPC framework developed by Google. It is an implementation of the RPC concept with several key advancements and features:

### Key Differences:

- Transport Protocol:
  - RPC (general): Can use various transport protocols like TCP, UDP, or even HTTP/1.1 (e.g., JSON-RPC).
  - gRPC: Exclusively uses HTTP/2 as its underlying transport protocol, which offers advantages like multiplexing (multiple requests over a single connection), header compression, and bidirectional streaming. 

- Serialization Format:
  - RPC (general): Can use various data formats like JSON, XML, or custom binary formats.
  - gRPC: Primarily uses Protocol Buffers (Protobuf) for data serialization, a language-agnostic, efficient binary format that results in smaller payload sizes and faster parsing. 

- Interface Definition Language (IDL):
  - RPC (general): May use various IDLs or no formal IDL at all, depending on the specific implementation.
  - gRPC: Uses Protocol Buffers as its IDL, where services and messages are defined in .proto files, which are then used to generate client and server-side code in various languages. 

- Streaming Capabilities:
  - RPC (general): Typically supports simple request-response interactions, with streaming often requiring specific implementation details.
  - gRPC: Offers built-in support for four types of service methods: unary (single request/response), server streaming, client streaming, and bidirectional streaming, enabling efficient real-time communication. 

- Performance:
  - RPC (general): Performance varies greatly depending on the chosen protocol, serialization format, and implementation.
  - gRPC: Generally offers superior performance due to its use of HTTP/2 and Protocol Buffers, which reduce latency and improve throughput. 

- Language Support:
  - RPC (general): Language support depends on the specific RPC framework.
  - gRPC: Provides robust multi-language support with code generation for a wide range of programming languages. 

- Built-in Features:
  - RPC (general): Security and other features often depend on the transport layer or require manual implementation.
  - gRPC: Includes built-in support for features like TLS/SSL for secure communication, and integrates with authentication mechanisms like Google OAuth 2.0. 

In essence, **gRPC is a highly optimized and feature-rich implementation of the RPC concept**, leveraging modern technologies like **HTTP/2** and **Protocol Buffers** to provide a robust and efficient framework for building distributed systems.


### Server Streaming vs Client Streaming

**Server streaming** allows a client to send one request to a server and receive a sequence of messages in response.
- Use Cases: Receiving real-time data, such as stock quotes or status updates, or when the server needs to process a request and send back multiple pieces of information over time.

**client streaming** enables a client to send a sequence of messages to the server, which then responds with a single message. 
- Use Cases: Sending a large amount of data from the client to the server for processing, such as sending location updates during a trip or uploading a batch of data for analytics. 

These concepts are fundamental to gRPC communication, offering alternatives to traditional **unary RPC (request/response)** for handling varying data flow needs, such as continuous updates or batched data submissions.  

### Links

- What is gRPC:
  - https://grpc.io/docs/what-is-grpc/introduction/
  - https://grpc.io/docs/what-is-grpc/core-concepts/#client-streaming-rpc

<br/>

## Object store

An object store is a data storage architecture that manages data as discrete units called "objects," each containing the data itself, 
a unique ID, and custom metadata. 

This approach is ideal for handling vast amounts of unstructured data like videos, images, and documents, offering scalability, cost-effectiveness, and simplified management compared to traditional block or file storage. 

Popular examples of object storage services include 
- Amazon S3
- Google Cloud Storage (from AWS)
- Microsoft Azure Blob Storage

## OLTP

Online Transaction Processing:
- a type of database system that processes and manages real-time transactions, such as online banking, e-commerce purchases,
  and ATM withdrawals.
- It is designed to handle a high volume of small, simultaneous operations like inserting, updating, and deleting data,
  prioritizing speed, concurrency, and data integrity through ACID principles (Atomicity, Consistency, Isolation, Durability)

## Idempotency

Uses idempotency keys to prevent duplicate charges if retries occur.

## PCI DSS compliance for payment handling

Payment Card Industry Data Security Standard
- a set of security requirements to protect cardholder data and reduce payment fraud.

## WebSockets/SSE (Server-Sent Events)

Push events to client, without using polling.

## Pessimistic Locking vs OCC (Optimistic Concurrency Control)

**Pessimistic Locking (preferred)** 

    How it works: Lock a seat immediately when a user selects it, preventing other users from reserving it until the lock expires.
    Advantages: Strong guarantee against double booking.
    Drawbacks: May cause unnecessary lock contention in high-traffic scenarios. Can suffer from attack of invalid selection.

**Optimistic Concurrency Control (OCC)**

    How it works: Allow multiple users to attempt reservation, but check version/timestamp before finalizing.
    Advantages: Less locking overhead; better for high read scenarios.
    Drawbacks: More failed checkouts under heavy contention.

## Message Queue

A message queue is a software component that stores messages in a queue to allow asynchronous communication between different parts of a system, such as between processes or threads. This allows senders and receivers to operate independently and at their own pace, improving system reliability and performance

### How it works

- A sender (producer) places a message onto the queue. 
- A receiver (consumer) retrieves and processes the message from the queue, typically in a First-In, First-Out (FIFO) order. 
- This decouples the sender and receiver, so the sender doesn't have to wait for the receiver to be ready before sending a message. 
- The receiver doesn't have to be available at the exact moment the message is sent; it can process messages later when it is ready. 

### Key benefits

- Decoupling: Services are independent and can be scaled or fail without affecting each other. 
- Asynchronous processing: Senders can continue their work without waiting for the receiver to finish processing the message. 
- Load leveling: Queues can handle spikes in traffic by storing messages until the receiver can process them at its own capacity. 
- Reliability: Message queues can prevent data loss, especially in distributed systems, by holding messages until they are successfully received.
- Fault tolerance: If a service fails, the messages remain in the queue to be processed once the service comes back online. 

### Use cases and examples

- Request-response patterns:
  A client sends a request to a queue, and the server processes it and sends a reply back through a separate queue. 
- Background tasks:
  A web application can put a request to generate a report into a queue, and a separate worker service can process it in the background. 
- Inter-thread communication:
  A program can use a message queue to pass data between different threads. 
- System integration:
  Queues allow applications and systems in different locations or on different platforms to communicate reliably, as shown in this YouTube video and explained on this AlgoMaster blog post. 

### Types of message queues

- Point-to-point:
  A message is sent to a specific queue and is consumed by only one receiver. 
- Publish/subscribe:
  A message is broadcast to multiple subscribers, making it a many-to-many model. 
- Priority queues:
  Messages are processed based on their assigned priority rather than strict FIFO order. 

### Popular message queue systems (Kafka, RabbitMQ)

- RabbitMQ:
  An open-source message broker known for reliability and support for AMQP, notes this AlgoMaster blog post.
- Apache Kafka:
  A distributed streaming platform that handles large volumes of data for real-time processing.
- Amazon SQS:
  A fully managed message queuing service from Amazon Web Services.
- Google Cloud Pub/Sub:
  A fully managed service for real-time analytics and event-driven applications from Google Cloud. 

#### Cons of popular message queues

- Redis is useful as a simple message broker but messages can be lost.
- RabbitMQ is popular but requires you to adapt to the 'AMQP' protocol and manage your own nodes.
- Amazon SQS is hosted but can have high latency and has the possibility of messages being delivered twice.


### Apache ZooKeeper 

1. https://en.wikipedia.org/wiki/Apache_ZooKeeper

An open-source, distributed coordination service for managing configuration, naming, and synchronization in large distributed systems. 

It provides a centralized service that enables reliable communication and coordination for applications, with features like distributed locks and leader election, and is used by technologies like Hadoop, HBase, and Spark

2. https://stackoverflow.com/questions/3662995/explaining-apache-zookeeper

- ZK is basically a data store which can be accessed using the ZK API's. 
- But it should NOT be used as a database.
- Only a small amount of data should be stored(usually in KB's). The upper limit is 1MB per znode.
- ZK is specifically built so that the distributed applications can communicate among each other.

Zookeeper is one of the best open source server and service that helps to reliably coordinates distributed processes. 
- Zookeeper is a CP system (Refer CAP Theorem) that provides (Strong) Consistency and Partition tolerance.
- Replication of Zookeeper state across all the nodes makes it an eventually consistent distributed service. 

Quorum simply means "these servers can vote upon who should be the leader".
- In ZK, servers form a quorum
- voting is based on majority.
- Majority is also the reason you should use an odd number of servers, to avoid "split brain".

3. https://www.geeksforgeeks.org/java/what-is-apache-zookeeper/

### Quorum and Split Brain

A quorum is a mechanism used to prevent split-brain scenarios in a cluster, where network issues cause the cluster to divide into two or more independent groups. 

A quorum ensures only one group can actively run services by using a consensus process, preventing data corruption from multiple sub-clusters writing to the same resources. A common way to ensure a quorum is by using an odd number of nodes or by introducing a separate witness/quorum node that acts as a tie-breaker. 

#### How quorum prevents split-brain

- Ensures a single active cluster: A quorum requires a majority of nodes to agree on which group should be the active one. If a network partition occurs, and a sub-cluster loses communication with the others, it cannot form a quorum and will not proceed with cluster operations, thus avoiding a split-brain situation.
- Provides a tie-breaker: In an even-numbered cluster, a network partition could lead to a deadlock where both sides have equal numbers of active nodes and can't decide who is in charge. A witness node or quorum device is added to break this tie, ensuring one side can win the election.
- Maintains data integrity: By preventing multiple active instances from running at the same time, quorum safeguards against data corruption and inconsistencies that would arise if two separate parts of the cluster tried to write to the same storage. 

How to implement quorum

- Use an odd number of nodes: A simple way to ensure a majority is to have an odd number of nodes. For example, in a 3-node cluster, 2 nodes are needed for a quorum.
- Add a witness/quorum node: Introduce a third, dedicated node that doesn't run applications but is part of the cluster's communication network to act as a tie-breaker. This can be a lightweight virtual machine or even a small device like a Raspberry Pi. 

### Service discovery

Service discovery is the process of automatically detecting devices and services on a network, which reduces the need for manual configuration. It works by using a service registry, which acts as a central catalog of all available services, allowing other services or clients to find and connect to them. This is essential in modern distributed and microservices architectures for handling dynamic environments.  

#### How it works

- Registration:
  A service registers itself with a service registry upon startup, providing its network location and other details. 
- Discovery:
  A client or another service queries the service registry to find the network address of the service it needs to communicate with. 
- Dynamic Updates:
  The service registry keeps a current list of all services, and it can automatically update it when a service is added, removed, or becomes unavailable. 

#### Benefits of service discovery

- Reduces manual configuration:
  Administrators don't have to manually update IP addresses and hostnames when services are added or moved.
- Increases resilience:
  Systems can automatically find healthy instances of a service, even if a particular instance fails or moves. 
- Supports dynamic environments:
  It is crucial for cloud-native and microservices architectures where services are constantly being created, scaled, and destroyed. 
- Enables dynamic load balancing:
  By knowing about multiple instances of a service, clients can distribute requests among them. 

#### Examples

- DNS (Domain Name Service): A traditional example where DNS maps hostnames to IP addresses. 
- Consul or ZooKeeper: Key-value stores often used in microservices to act as a service registry. 
- Kubernetes service discovery: Built into Kubernetes to allow pods to discover and communicate with each other. 


