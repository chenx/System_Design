# Uber

https://dev.to/karanpratapsingh/system-design-uber-56b1

## Requirements

### Functional requirements

We will design our system for two types of users: Customers and Drivers.

Customers

    Customers should be able to see all the cabs in the vicinity with an ETA and pricing information.
    Customers should be able to book a cab to a destination.
    Customers should be able to see the location of the driver.

Drivers

    Drivers should be able to accept or deny the customer requested ride.
    Once a driver accepts the ride, they should see the pickup location of the customer.
    Drivers should be able to mark the trip as complete on reaching the destination.

### Non-Functional requirements

    High reliability.
    High availability with minimal latency.
    The system should be scalable and efficient.

### Extended requirements

    Customers can rate the trip after it's completed.
    Payment processing.
    Metrics and analytics.

### Estimation and Constraints 

Traffic 

## Data model design 

customers

This table will contain a customer's information such as name, email, and other details.

drivers

This table will contain a driver's information such as name, email, dob and other details.

trips

This table represents the trip taken by the customer and stores data such as source, destination, and status of the trip.

cabs

This table stores data such as the registration number, and type (like Uber Go, Uber XL, etc.) of the cab that the driver will be driving.

ratings

As the name suggests, this table stores the rating and feedback for the trip.

payments

The payments table contains the payment-related data with the corresponding tripID.

## Database

## API design

Request a Ride 

requestRide(customerID: UUID, source: Tuple<float>, destination: Tuple<float>, cabType: Enum<string>, paymentMethod: Enum<string>): Ride

Cancel a Ride

cancelRide(customerID: UUID, reason?: string): boolean

Accept or Deny the Ride

acceptRide(driverID: UUID, rideID: UUID): boolean
denyRide(driverID: UUID, rideID: UUID): boolean

Start or End the Trip

startTrip(driverID: UUID, tripID: UUID): boolean
endTrip(driverID: UUID, tripID: UUID): boolean

Rate the Trip

rateTrip(customerID: UUID, tripID: UUID, rating: int, feedback?: string): boolean

## High-level design

### Services

Architecture

We will be using microservices architecture since it will make it easier to horizontally scale and decouple our services. Each service will have ownership of its own data model. Let's try to divide our system into some core services.

Customer Service

This service handles customer-related concerns such as authentication and customer information.

Driver Service

This service handles driver-related concerns such as authentication and driver information.

Ride Service

This service will be responsible for ride matching and quadtree aggregation. It will be discussed in detail separately.

Trip Service

This service handles trip-related functionality in our system.

Payment Service

This service will be responsible for handling payments in our system.

Notification Service

This service will simply send push notifications to the users. It will be discussed in detail separately.

Analytics Service

This service will be used for metrics and analytics use cases.

What about inter-service communication and service discovery?

Since our architecture is microservices-based, services will be communicating with each other as well. Generally, REST or HTTP performs well but we can further improve the performance using gRPC which is more lightweight and efficient.

Service discovery is another thing we will have to take into account. We can also use a service mesh that enables managed, observable, and secure communication between individual services.

### Location Tracking 

Pull model

Push model
- Web socket
- SSE (Server-Sent Events)

### Ride Matching

- SQL
  - SELECT * FROM locations WHERE lat BETWEEN X-R AND X+R AND long BETWEEN Y-R AND Y+R
  - this is not scalable, and performing this query on large datasets will be quite slow
- Geohashing
- Quadtrees
- Race condition
  - To avoid this can wrap our ride matching logic in a Mutex to avoid any race conditions.
- Notifications

#### Geohashing

Geohash is a hierarchical spatial index that uses Base-32 alphabet encoding, the first character in a geohash identifies the initial location as one of the 32 cells. This cell will also contain 32 cells. This means that to represent a point, the world is recursively divided into smaller and smaller cells with each additional bit until the desired precision is attained. The precision factor also determines the size of the cell.

For example, San Francisco with coordinates 37.7564, -122.4016 can be represented in geohash as 9q8yy9mf.

##### Base32

https://en.wikipedia.org/wiki/Base32

- One way to represent Base32 numbers in human-readable form is using digits 0–9 followed by the twenty-two upper-case letters A–V.
  - However, many other variations are used in different contexts
  
- The most widely used[citation needed] base32 alphabet is defined in RFC 4648 §6 and the earlier RFC 3548 (2003).
  - The scheme was originally designed in 2000 by John Myers for SASL/GSSAPI
  - It uses an alphabet of A–Z, followed by 2–7.
  - The digits 0, 1 and 8 are skipped due to their similarity with the letters O, I and B (thus "2" has a decimal value of 26). 

#### Quadtrees

A Quadtree is a tree data structure in which each internal node has exactly four children. They are often used to partition a two-dimensional space by recursively subdividing it into four quadrants or regions. Each child or leaf node stores spatial information. Quadtrees are the two-dimensional analog of Octrees which are used to partition three-dimensional space.

### Detailed design 

- Data Partitioning
- Metrics and Analytics
- Caching (Redis, Memcache)

### Scaling issues

Let us identify and resolve bottlenecks such as single points of failure in our design:

    "What if one of our services crashes?"
    "How will we distribute our traffic between our components?"
    "How can we reduce the load on our database?"
    "How to improve the availability of our cache?"
    "How can we make our notification system more robust?"

To make our system more resilient we can do the following:

    Running multiple instances of each of our services.
    Introducing load balancers between clients, servers, databases, and cache servers.
    Using multiple read replicas for our databases.
    Multiple instances and replicas for our distributed cache.
    Exactly once delivery and message ordering is challenging in a distributed system, we can use a dedicated message broker such as Apache Kafka or NATS to make our notification system more robust.


# Uber (Design 2)

https://www.hellointerview.com/learn/system-design/problem-breakdowns/uber

# Uber (Design 3)

https://medium.com/@lazygeek78/system-design-of-uber-lyft-549963c816b4

# Uber (Design 4)

https://www.geeksforgeeks.org/system-design/system-design-of-uber-app-uber-system-architecture/



