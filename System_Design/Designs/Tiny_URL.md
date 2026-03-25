# Tiny URL

## Solution 1

### 1. Use cases

- Shortening service (full URL -> short URL)
  - Assumptions:
    - full url: 512 bytes
    - short url: 6 bytes: 62^6 =~ 64^6 = 2^36 = 64 billion
      BASE 62: [a-zA-Z0-9]{6}: BASE62(MD5(RAW_URL + salt))[:6]
    - If 7 bytes, then it's ~3.5 trillion urls for use
- Look up service (short URL -> full URL)
- Redirection service
- Other requirements
  - expiration time of short URL
  - avoid reserved words

### 2. Abstract design, sketch components

user /client <-> web UI <-> web server <-> Database

Database schema: id, raw_url, tiny_url, salt, expiration_time

### 3. Core components design

Shortening service
    - full url: 512 bytes
    - short url: 6 bytes: 62^6 =~ 64^6 = 2^36 = 64 billion
      BASE 62: [a-zA-Z0-9]{6}: BASE62(MD5(RAW_URL + salt))[:6]
    - avoid collision: change salt if collision exists.

Look up service: create index on tiny_url

Redirection service: 301, 302

### 4. Bottleneck and scaling

Address space:
1 million (2^20) new URL / day: 2^36 / 2^20 = 2^16 = 64k days = 64k / 365 years =~ 192 years to use all addresses

Read:
100 million reads / day:  100 * 2^20 * (500 + 6) =~ 50 billion bytes / day = 50 GB / day

Write: Assume Read / Write ratio: 100 : 1
0.5 GB /day, 180 GB / year.

1 machine can have 10 TB Harddrive, so storage is not a problem. 

HD, RAM, CPU no problem in general, bandwidth may be a problem.

MySQL database should be fine. Can use memcached / redis as in-memory cache.

For data safety: have 3 copies: 3*2 servers in 3 different places; in each place: 1 master, 1 slave.


### 5. Scaling discussion

- V/H scaling
- cache (for read): redis / memcached in memory cache
- load balance
- sharding
- multiple server: round robin / consistent hash
- replication / redundancy
- read/write separation
- locality

<br/>

## Solution 2

https://dev.to/karanpratapsingh/system-design-url-shortener-10i5



