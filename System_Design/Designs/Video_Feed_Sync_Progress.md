# Video Feed Sync Progress

Synchronizing watch progress across clients (web, mobile, TV, etc.) is a classic system-design problem found in apps like Netflix, YouTube, and Spotify (for playback position).
Below is a clean, structured, high-level system design.

## ✅ Requirements

### Functional

- Track watch progress per user per video (e.g., timestamp, percentage, completion).
- Sync progress almost in real-time across all user devices.
- Allow resuming from the last watched position.
- Propagate updates efficiently but avoid overloading server.
- Handle offline usage and conflict resolution.

### Non-Functional

- High write rate (clients frequently update playback position).
- High read rate (resuming on new devices).
- Low latency propagation.
- Durability & correctness (no lost progress).
- Efficient storage.

## 🧱 High-Level Architecture

```
Clients (Web / iOS / Android / TV)
        |
        v
   API Gateway / Load Balancer
        |
        v
Progress Service  <---->  Message Broker (Kafka/SQS)
        |
        v
   Data Store (NoSQL / Redis / Postgres)
        |
        v
Notification/Sync Service (WebSockets/push)
```

## ⭐ Key Components and Design Decisions

### 1. Client Behavior

Clients should not spam the server every second. Instead:

- Send progress updates every N seconds (e.g., 5–15s).
- Send update on:
  - pause
  - stop
  - app close
  - video finished
  - Store local progress to recover if offline.

Payload example:
```
{
  "userId": "123",
  "videoId": "abc",
  "positionSec": 1820,
  "durationSec": 3600,
  "completed": false,
  "deviceId": "ios11"
}
```

### 2. Backend Progress Service

Responsibilities:

- Write the user's progress.
- Read the latest progress for resume.
- Apply conflict resolution.
- Push changes to other devices (optional real-time).

This service must be idempotent and accept frequent writes.

### 3. Data Storage Options

#### Option A: NoSQL (DynamoDB / MongoDB / Cassandra)

Good for high-frequency updates and simple schemas.

Schema example:
```
Partition Key: userId
Sort Key: videoId
Attributes: lastPosition, updatedAt, deviceId
```

Pros: horizontal scale, high write throughput

Cons: limited transaction support, eventual consistency

#### Option B: Redis (as primary or cache)

Used when you want low-latency reads.

- Cache current position in Redis
- Periodically write to durable DB
- Set TTL so old data decays

#### Option C: SQL (Postgres)

If scale is moderate.

Schema:
```
user_id + video_id (PRIMARY KEY)
position_sec
duration_sec
updated_at
device_id
```

### 4. Conflict Resolution

Users may watch on multiple devices simultaneously.

Rule of thumb:
- Use latest updated timestamp (last write wins)
- But ignore regressions:
  - If new position < old position by a large amount → reject (avoid accidental rewind).

Example:
```
If newPosition > oldPosition OR newUpdatedAt > oldUpdatedAt:
    accept
Else:
    ignore
```

### 5. Real-Time Sync Across Devices

If you want live syncing (e.g., YouTube app remembers instantly):

Approaches:

#### A. WebSockets

Clients subscribe to their user-level channel.

When progress updates → backend publishes via WebSocket server.

#### B. Push Notifications

For mobile/tablet devices that are offline or backgrounded.

#### C. Polling fallback

Slow polling every 30–60 seconds for devices without real-time support.

### 6. Message Broker (Optional)

If progress updates need fan-out or async processing:

Kafka, Kinesis, or SQS

Used to:
- send updates to sync service
- update analytics/watch-history service
- trigger “continue watching” recommendations

### 7. Offline Handling

Client-side:

- Save progress locally every few seconds
- When online:
  - push local progress
  - server resolves conflicts

Server-side:
- Simple last-write-wins strategy

Consider merging:
- E.g., if two devices watched different segments (rare for video), choose max position.

### 8. Scaling Considerations

- Cache heavily (Redis)
- Shard by user or video
- Batch writes if needed
- Ensure idempotency to handle retries
- Use compression for analytics events if streaming raw watch events

### 📝 Example Flow

1. User watches on Mobile

Mobile sends update { videoId: A, position: 120s }.

2. Backend stores in Redis + DB

Write-through: Redis → DB.

3. Backend publishes to WebSocket channel user:123

TV app and web app receive update and update UI.

4. User switches to TV

TV requests /progress?video=A → gets { position: 120s }.

Playback starts from correct position.

## 🎯 Summary

To synchronize watch progress reliably:

✔ Health Checks

Clients send periodic & event-based progress updates.

✔ Storage

Use Redis + NoSQL/SQL for fast reads and durable writes.

✔ Conflict resolution

Last-write-wins but avoid regressions.

✔ Multiple devices

Use WebSockets/push for real-time sync.

✔ Offline mode

Local buffering + conflict reconciliation.

This design scales to millions of users and billions of progress updates.
