# Search Autocomplete

## Clarifying questions

- Is the matching only supported at the beginning of a search query or in the middle as well? Beginning
- How many autocomplete suggestions should the system return? 5
- How does the system know which 5 suggestions to return? popularity, decided by the historical query frequency.
- Does the system support spell check? No
- Are search queries in English? Yes
- Do we allow capitalization and special characters? No
- DAU? 10 million DAU.

### Summary of the requirements

- Fast response time: As a user types a search query, autocomplete suggestions must show up fast enough.
  - Facebook’s autocomplete system: the system needs to return results within 100 ms.
- Relevant: Autocomplete suggestions should be relevant to the search term.
- Sorted: Results returned are sorted by popularity or other ranking models.
- Scalable: The system can handle high traffic volume.
- Highly available: available and accessible when part of the system is offline, slows down, or experiences unexpected network errors.

### Back of the envelope estimation

- Assume 10 million daily active users (DAU).
- An average person performs 10 searches per day.
- 20 bytes of data per query string:
- For every character entered into the search box, a client sends a request to the backend
  for autocomplete suggestions. On average, 20 requests are sent for each search query
- ~24,000 query per second (QPS) = 10,000,000 users * 10 queries / day * 20 characters / 24 hours / 3600 seconds.
- Peak QPS = QPS * 2 = ~48,000
- Assume 20% of the daily queries are new. 10 million * 10 queries / day * 20 byte per query * 20% = 0.4 GB.
  - This means 0.4GB of new data is added to storage daily.

<br/>

## High Level Design

### Data gathering service

It gathers user input queries and aggregates them in real-time.

### Query service

Given a search query or prefix, return 5 most frequently searched terms.

- Query: it stores the query string.
- Frequency: it represents the number of times a query has been searched.

## Deep Dive

- Trie data structure (prefix tree)
  - fetching the top 5 search queries from a relational database is inefficient.
  - The data structure trie (prefix tree) is used to overcome the problem.
    - Basic trie data structure stores characters in nodes. To support sorting by frequency, frequency info is included in nodes.
    - Limit the max length of a prefix
    - Cache top search queries at each node

- Data gathering service
  - Update data in real time is not scalable
    - Users may enter billions of queries per day. Updating the trie on every query significantly slows down the query service.
    - Top suggestions may not change much once the trie is built. Thus, it is unnecessary to update the trie frequently.
  - Analytics Logs.
    - It stores raw data about search queries. Logs are append-only and are not indexed.
  - Aggregators
  - Aggregated Data
  - Workers
  - Trie Cache
    - a distributed cache system that keeps trie in memory for fast read
  - Trie DB
    - persistent storage
    - 2 options
      - Document store: e.g., mongoDB
      - Key-value store. A trie can be represented in a hash table form.
     
- Query service
  - Query service requires lightning-fast speed. We propose the following optimizations
    - AJAX request
    - Browser caching
    - Data sampling
  - Trie operations
    - Create
    - Update
      - Option 1: Update the trie weekly. Once a new trie is created, the new trie replaces the old one.
      - Option 2: Update individual trie node directly.
        - We try to avoid this because it is slow. However, if the size of the trie is small, it is acceptable.
    - Delete
      - remove hateful, violent, sexually explicit, or dangerous autocomplete suggestions

- Scale the storage
  - solve the scalability issue when the trie grows too large to fit in one server
  - a naive way to shard is based on the first character
  - To mitigate the data imbalance problem, we analyze historical data distribution pattern and apply smarter sharding logic.

- Trie operations

## Others

Interviewer: How do you extend your design to support multiple languages?
- To support other non-English queries, we store Unicode characters in trie nodes.

Interviewer: What if top search queries in one country are different from others?
- In this case, we might build different tries for different countries. To improve the response time, we can store tries in CDNs.

Interviewer: How can we support the trending (real-time) search queries?
- Assuming a news event breaks out, a search query suddenly becomes popular.
- Building a real-time search autocomplete is complicated. Ideas:
  - Reduce the working data set by sharding.
  - Change the ranking model and assign more weight to recent search queries.
  - Data may come as streams, so we do not have access to all the data at once. Streaming data means data is generated continuously.
    - Stream processing requires a different set of systems:
      - Apache Hadoop MapReduce,
      - Apache Spark Streaming,
      - Apache Storm
      - Apache Kafka

