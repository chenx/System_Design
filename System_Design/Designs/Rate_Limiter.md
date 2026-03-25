# Rate Limiter

## Algorithms

- Token bucket
  - a token is consumed for each request (come and serve/process)
  - new tokens are refilled at next time interval
  - The token bucket algorithm takes two parameters:
    - Bucket size: the maximum number of tokens allowed in the bucket
    - Refill rate: number of tokens put into the bucket every second
- Leaking bucket
  - similar to the token bucket except that requests are processed at a fixed rate.
  - process
    - When a request arrives, the system checks if the queue is full. If it is not full, the request is added to the queue.
    - Otherwise, the request is dropped.
    - Requests are pulled from the queue and processed at regular intervals.
  - Leaking bucket algorithm takes the following two parameters:
    - Bucket size: it is equal to the queue size. The queue holds the requests to be processed at a fixed rate.
    - Outflow rate: it defines how many requests can be processed at a fixed rate, usually in seconds.
- Fixed window counter
  - Process
    - The algorithm divides the timeline into fix-sized time windows and assign a counter for each window.
    - Each request increments the counter by one.
    - Once the counter reaches the pre-defined threshold, new requests are dropped until a new time window starts
  - Each bucket has a limit
  - Cons: at the edge of the bucket, more requests may be handled
- Sliding window log
  - similar to HitCounter
  - Pros:
    - Rate limiting implemented by this algorithm is very accurate. In any rolling window, requests will not exceed the rate limit.
  - Cons:
    - The algorithm consumes a lot of memory because even if a request is rejected, its timestamp might still be stored in memory.
- Sliding window counter
  - The sliding window counter algorithm is a hybrid approach that combines the fixed window counter and sliding window log.
  - requests = Requests in current window + requests in the previous window * overlap percentage of the rolling window and previous window
  - Pros
    - It smooths out spikes in traffic because the rate is based on the average rate of the previous window.
    - Memory efficient.
  - Cons
    - It only works for not-so-strict look back window.

<br/>

## High level design

Where: server side (not client side)
