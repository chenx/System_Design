# Chat System

E.g., wechat, whatsapp, line, facebook messenger, google hangout, discord

## Clarifying questions

- Support 1:1 or group chat? Both
- Mobile or web app? Both
- Scale? 50 million DAU
- Group chat member limit? 100
- Features: 1 on 1 chat, group chat, online indicator, text message only
- Message size limit? 100,000 bytes / message
- e2e encryption? Not needed for now
- How long to store the chat history? Forever

Features discussed here:
- A one-on-one chat with low delivery latency
- Small group chat (max of 100 people)
- Online presence
- Multiple device support. The same account can be logged in to multiple accounts at the same time.
- Push notifications
- 50 million DAU

## High Level Design

Sender -> Chat Service (Store / Relay message) -> Receiver

Polling
- The client periodically asks the server if there are messages available
- Inefficient

Long polling
- a client holds the connection open for new messages or a timeout threshold has been reached
- cons
  - A server has no good way to tell if a client is disconnected.
  - Inefficient. If a user does not chat much, long polling still makes periodic connections after timeouts.

WebSocket
- connections are persistent
- bidirectional communication

### Categories

the chat system is broken down into three major categories:
1) stateless services
   - traditional public-facing request/response services, used to manage the login, signup, user profile, etc
2) stateful services
   - The only stateful service is the chat service
3) third-party integration
   - For a chat app, push notification is the most important third-party integration

### Scalability

At 1M concurrent users, assuming each user connection needs 10K of memory on the server, it only
needs about 10GB of memory to hold all the connections on one box.

### Storage

An important decision is to decide on the right type of database to use: RDBMS or NoSQL

Two types of data exist in a typical chat system. 

1) The first is generic data, such as user profile, setting, user friends list.

- These data are stored in robust and reliable relational databases.
- Replication and sharding are common techniques to satisfy availability and scalability requirements.

2) The second is unique to chat systems: chat history data.

- The amount of data is enormous for chat systems.
- Only recent chats are accessed frequently
- Although very recent chat history is viewed in most cases, users might use features that
  require random access of data, such as search, view your mentions, jump to specific
  messages
- The read to write ratio is about 1:1 for 1 on 1 chat apps

Recommend key-value stores for the following reasons:
- Key-value stores allow easy horizontal scaling.
- Key-value stores provide very low latency to access data.
- Relational databases do not handle long tail of data well. When the indexes grow large, random access is expensive.
- Key-value stores are adopted by other proven reliable chat applications.
  - For example, both Facebook messenger and Discord use key-value stores.
  - Facebook messenger uses HBase, and Discord uses Cassandra.

### Data models

Message table for 1 on 1 chat
- message_id
- from
- to
- content
- create_at

Message table for group chat
- channel_id
- message_id
- user_id
- content
- create_at

<br/>

## Deep Dive

### Service discovery

Recommend the best chat server for a client based on the criteria like geographical location, server capacity.

E.g. Zookeeper

### Message flows

1 on 1 chat flow

Message synchronization across multiple devices

### Small group chat flow

### Online presence

### User login

### User logout

### User disconnection

### Online status fanout

<br/>

## Other things

- Extend the chat app to support media files such as photos and videos. Media files are
  significantly larger than text in size. Compression, cloud storage, and thumbnails are
  interesting topics to talk about.
- End-to-end encryption. Whatsapp supports end-to-end encryption for messages. Only the
  sender and the recipient can read messages.
- Caching messages on the client-side is effective to reduce the data transfer between the
  client and server.
- Improve load time. Slack built a geographically distributed network to cache users’ data,
  channels, etc. for better load time [10].
- Error handling.
  - The chat server error
  - Message resent mechanism
 


