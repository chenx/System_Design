# News Feed System

## Clarifying questions

- Is this a mobile app? Or a web app? Or both? Both
- Features? A user can publish a post and see her friends’ posts on the news feed page.
- Feed is sorted by reverse chronological order.
- How many friends can a user have? 5000
- Traffic? 10 million DAU
- Can feed contain images, videos, or just text? It can contain media files, including both images and videos.

## High level design

2 flows:

- Feed publishing:
  - when a user publishes a post, corresponding data is written into cache and database.
  - A post is populated to her friends’ news feed.
- Newsfeed building:
  - assume the news feed is built by aggregating friends’ posts in reverse chronological order

### Newsfeed APIs

APIs are HTTP based that allow clients to perform actions, which include posting a status,
retrieving news feed, adding friends, etc. 

We discuss two most important APIs: 
- feed publishing API
  - POST /v1/me/feed
  - params
    - content: content is the text of the post.
    - auth_token: it is used to authenticate API requests
- news feed retrieval API.
  - GET /v1/me/feed
  - params
    - auth_token: it is used to authenticate API requests 

### Feed publishing

User / Client -> Load Balancer -> Web servers -> 
1) Post service (Post cache, DB)
2) Fan out service (News feed cache)
3) Notification service

### Newsfeed building

Use / Client -> Load Balancer -> Web servers -> 
1) New feed service (New feed cache)

<br/>

## Deep Dive

### Feed publishing deep dive

Web servers
- authenticating
- rate limiting

Fanout service
- Fanout on write
  - news feed is pre-computed during write time
  - pros
    - The news feed is generated in real-time and can be pushed to friends immediately.
    - Fetching news feed is fast because the news feed is pre-computed during write time
  - cons
    - If a user has many friends, fetching the friend list and generating news feeds for all of them are slow (hotkey problem)
    - For inactive users, pre-computing news feeds waste computing resources.
- Fanout on read
  - news feed is generated during read time
  - pros
    - For inactive users, fanout on read works better because it will not waste computing resources on them.
    - Data is not pushed to friends so there is no hotkey problem.
  - cons
    - Fetching the news feed is slow as the news feed is not pre-computed.

We use a hybrid method:
- use a push model for the majority of users (not many friends or followers).
- For celebrities or users who have many friends/followers, we let followers pull news content on-demand to avoid system overload

Fanout service
1) get friends list (from graph database)
2) get friends data (user cache, user database)
3) message queue -> fanout workers -> news feed cache

### News feed retrieval deep dive

News feed service
1) News feed cache
2) Post cache (post database)
3) User cache (user database)

### Cache architecture

5 layers:
1) News feed
2) Content (hot cache, normal)
3) Social graph (follower, following)
4) Action (liked, replied, others)
5) Counters (liked counter, replied counter, others counter)

## Others

Scaling the database:
- Vertical scaling vs Horizontal scaling
- SQL vs NoSQL
- Master-slave replication
- Read replicas
- Consistency models
- Database sharding

Other talking points:
- Keep web tier stateless
- Cache data as much as you can
- Support multiple data centers
- Lose couple components with message queues-
- Monitor key metrics.
  - e.g., QPS during peak hours and latency

