# Design Tiktok

## Design 1: Designing TikTok | System Design

https://www.geeksforgeeks.org/system-design/designing-tiktok-system-design/

### Requirements

#### Functional

    User Profile
    Uploading and streaming short video clips.
    Creating and sharing video content.
    Following user-profiles and exploring curated video feeds.
    Liking, disliking, and commenting on videos.
    Discovering new videos based on personalized recommendations.

#### Non-functional

    Performance: Specify the maximum acceptable time for the app to respond to user interactions, 
        such as uploading a video, loading the For You Page, or applying filters/effects.
    Security: Specify encryption standards for protecting user data during transmission and storage.
    Storage Capacity: Specify the maximum amount of data (videos, images, user accounts) the system 
        should be able to store.
    Code maintainability: Specify coding standards and practices to ensure that the codebase 
        remains maintainable over time.
    Reilable: Highly available and reliable system
    Latency: Low latency for real-time video streaming
    Scalabiltiy: Highly scalable to handle large read/write volumes
    Streaming & Comments: Eventually consistent streams and comments

#### Capacity Estimation

Traffic estimation

    Monthly Active Users (MAU): 1 billion
    Daily Active Users (DAU): 50% of MAU = 500 million
    Daily Video Uploads: 50 Million
    Requests Per Second (RPS): 500 million/ 24*60*60 = 5,787
    Peak RPS: 2*RPS: 11,574.

Storage estimation

    Average user profile size: 1MB
    Total User Profiles Storage: 1 billion * 1 MB = 1 Petabyte (PB)
    Average Video Metadata: 500 KB
    Daily Video Metadata Storage: 50 million * 500 KB = 25 Terabytes (TB)
    Monthly Video Metadata Storage: 25 TB * 30 days = 750 TB
    Average Video size: 20MB.
    Total Video Streams Storage: 50 million * 20 MB = 1 Petabyte (PB) daily
    Monthly Video Streams Storage: 1 PB * 30 days = 30 PB

Interaction data

    Daily Interactions Storage: 500 million * 10 * 100 bytes = 500 GB
    Monthly Interactions Storage: 500 GB * 30 days = 15 TB

Bandwidth 

    Assuming Average Video Size: 20 MB (high-definition content)
    Daily Video Streaming Bandwidth: 50 million * 20 MB = 1 PB daily
    Monthly Video Streaming Bandwidth: 1 PB * 30 days = 30 PB

### Use Case Diagram

- Unauthorized
- Authorized
  - Viewer
  - Content creator

### High Level Design

Client / Users -> Load Balancer -> API Servers -> Upload Service -> Database -> Cache -> Data Streaming Services

#### Video Uploading Process:

#### Video Streaming Process:

#### Database Design

Users -> 1) Social Graph, 2) Video (Likes, Dislikes, Comments)

![Database Design of Tiktok](https://media.geeksforgeeks.org/wp-content/uploads/20231228231634/Database-Design-of-TikTok.png "Tiktok Database diagram")

User:
```
{
    UserID (Primary Key)
    Username
    Email
    Password (Hashed)
    Profile Picture URL
    Bio
    Registration Date
}
```

Social graph:
```
{
    UserID (Primary Key)
    Follower IDs (Array/Map)
    Following IDs (Array/Map)
    Additional Graph Information
}
```

Video:
```
{
    VideoID (Primary Key)
    UserID (Foreign Key to Users)
    Title
    Description
    Upload Date
    Views Count
    Duration
    Other Metadata
}
```

Interactions:
```
{
    LikeID (Primary Key)
    UserID (Foreign Key to Users)
    VideoID (Foreign Key to Videos)
    Timestamp
    Additional Like Information
}

{
    DislikeID (Primary Key)
    UserID (Foreign Key to User)
    VideoID (Foreign Key to Video)
    Timestamp
}

{
    CommentID (Primary Key)
    UserID (Foreign Key to Users)
    VideoID (Foreign Key to Videos)
    Comment Text
    Timestamp
    Additional Comment Information
}
```

#### Types of Databases used in TikTok Design

RDBMS

NoSQL

Blob Storage

#### API

User Management
- 'POST /api/users/register'
- 'POST /api/users/login'
- 'POST /api/videos/upload'
- 'POST /api/videos/:videoID/like'
- 'POST /api/videos/:videoID/dislike'
- 'POST /api/videos/:videoID/comment'
- 'GET /api/videos/:videoID'
- 'GET /api/videos/:userID'
- 'GET /api/videos/trending'
- 'POST /api/users/:userID/follow'
- 'POST /api/users/:userID/unfollow'
- 'POST /api/messages/send'
- 'GET /api/feed/:userID'
- 'GET /api/discover'

#### Microservices

- Authentication Services
- Video Upload Services
- Primary Storage (Blob Storage)
- Encoder/Transcoder Services
- Recommendation Services
- Fanout Services
- Notification Services
- Cache


<br/>

### Low Level Design

#### User Authentication

#### User Profile Handling

#### Video Upload

#### Comment and Interaction

#### Cache

#### Content Optimization

#### Notification

#### Messaging

#### Recommendation

#### Feed Generation

#### Structured Database

#### NoSQL Database

#### Moderation Services

#### Security Measures

<br/>

### Scalability

- Distributed Architecture:
  - TikTok implements a distributed system with microservices architecture.
  - This modular structure allows for scaling individual components independently, minimizing the impact of scaling on the entire system.
- Horizontal Scaling:
  - Databases and services are designed to horizontally scale, spreading the load across multiple servers.
  - Sharding techniques and partitioning data ensure that the system can handle increasing data volumes effectively.
- Caching Strategies:
  - To reduce the load on databases, TikTok utilizes caching for frequently accessed content.
  - This strategy optimizes performance by storing data closer to users, ensuring faster retrieval and reduced server load.
- Load Balancing:
  - Load balancers distribute incoming traffic evenly across multiple servers.
  - This ensures that no single server becomes overwhelmed, maintaining system performance during high traffic periods.
- Elastic Resources:
  - The use of cloud-based infrastructure allows TikTok to scale resources dynamically based on demand.
  - Elasticity in computing resources ensures the platform can seamlessly handle fluctuations in user activity without compromising performance.
- Optimized Algorithms:
  Algorithms for recommendation engines and content delivery are designed for scalability.
  They efficiently process large datasets and user interactions, adapting to increasing data inputs without sacrificing performance.


<br/>

## Design 2: Building TikTok-Style Video Feed for 100 Million Users

https://itnext.io/system-design-building-tiktok-style-video-feed-for-100-million-users-2b3e332678d8

https://www.reddit.com/r/programming/comments/1krrumv/system_design_building_tiktokstyle_video_feed_for/
 

## Requirements

### Requirements

At a high-level, our product would consist of:

- Client — Mobile devices, browsers to view the user’s video feed.
  - The client device would also gather the user’s engagement and send it to the backend servers.
- Server — The server would be responsible for intelligent feed generation.
  - It would manage and process the user’s engagement with the different videos.

#### Functional Requirements (FRs)

- FR-1 — The system provides an interface for the clients to fetch the video feed.
- FR-2 — The system must allow the users to scroll through the different videos in their feed. (similar to Instagram reels, Youtube shorts)
- FR-3 — It must gather the different user signals such as watch time, likes, comments, shares, etc.

#### Non-functional Requirements (NFRs)

- NFR-1 — a) The feed generation must be performant and render within 500 ms. b) It must prioritise the fresh content to keep the users engaged.
- NFR-2 — a) Videos should be rendered quickly while scrolling. b) The transition must be seamless while scrolling(no buffering)
- NFR-3 — a) Gathering signals must be done in a cost-efficient manner. b) The feed must be updated real-time based on the signals collected.

#### Entities

User

The user is the central entity of the design. Every user would be served a custom feed. The feed would be influenced by factors such as platform engagement, watched videos, etc.

Video

The videos would be uploaded by the creators. The platform would store the video metadata along with the actual videos. (We will not dive into how system would handle and process uploaded videos)

Interactions

This includes metrics such as the video watch time, whether the user liked the video, count of video shares, comments, etc.


### High-level Design

Client <=> Server 

Server: Video Feed Service <=> 1) Feed Ranking Service (Fetch/Update), 2) Database


#### APIs

FR-1: Fetch video feed
- GET /api/feed?limit=10

FR-2: Scroll Through Videos
- GET /api/scroll?current_video_id=abcd1234&direction=next

FR-3: Gather User Engagement
- POST /api/engagements/


### Bottlenecks, Improvements and Optimizations

Video scroll (NFR-2)
- Quick rendering during scroll.
- Seamless transition between scroll.

Pagination and Caching
- Pagination: The server would fetch a fixed number of videos and return it back to the clients as feed.
- Caching: Clients would fetch and cache the video feed. As the user scrolls, the videos would be served from the cache.

Video rendering

Video freshness (NFR-1)

Collecting user engagement/signals (NFR-3)

Two approaches to gather the user engagement:
- Send engagement on every scroll.
- Batch and send engagement after 5–10 scrolls.

![Pros and Cons Gather User Engagement](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*RTIHGnE4b99bEuGyfMAo-A.png "Pros and Cons Gather User Engagement")

<br/>

## Design 3: Design Of TikTok

https://medium.com/@lazygeek78/design-of-tiktok-e2da18e17f2b

### Functional requirement

### API

### Database Schema

### High Level Design

![High Level Design](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*egh1GUgRsiyDLmQqknmxcw.png)

### Low Level Design

Tik-tok recommendation system has three component.

- Bigdata framework. Realtime streaming, processing, data computing and data storage.
- Machine learning. Build models and generate recommendation to suit individual preferences.
- Microservice based architecture.

![](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*ChqELT6shttvDN8a9xguEA.png "")  

### Bottlenecks

- Storage bottleneck for storing user metadata
- The real-time feeding of videos
- Every second, users upload massive amounts of video content that needs

### Optimizations

- Optimizing video streaming. TikTok pre-fetches videos in the user’s queue while using lazy loading techniques to minimize data usage and ensure instant video playback as users scroll.
- TikTok employs a multi-CDN strategy to handle different regions and optimize traffic routing, ensuring reliable and fast video delivery globally. It also uses extensively the edge caching for faster delivery of some non-frequent accessed data.
- TikTok uses advanced compression algorithms for video storage and transmission, reducing file sizes without compromising quality. It uses lossless compression techniques for metadata and minimal loss in quality for video content (using formats like H.265/HEVC or AV1).
- TikTok employs AI-powered content moderation systems to flag inappropriate content in real-time.

