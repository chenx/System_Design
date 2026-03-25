# Design Youtube

## Clarifying questions

- Features? Ability to upload a video and watch a video.
- Clients supported? Mobile apps, web browsers, and smart TV.
- DAU? 5 million
- Average daily time spent? 30 minutes.
- Support international users? Yes
- Supported vidoe resolution? most of the video resolutions and formats
- Is encryption required? Yes
- File size requirement? focuses on small and medium-sized videos. The maximum allowed video size is 1GB.
- Can we leverage some of the existing cloud infrastructures provided by Amazon, Google, or Microsoft? Some

Summary:
- Ability to upload videos fast
- Smooth video streaming
- Ability to change video quality
- Low infrastructure cost
- High availability, scalability, and reliability requirements
- Clients supported: mobile apps, web browser, and smart TV

Calculations:
- Assume the product has 5 million daily active users (DAU).
- Users watch 5 videos per day.
- 10% of users upload 1 video per day.
- Assume the average video size is 300 MB.
- Total daily storage space needed: 5 million * 10% * 300 MB = 150TB
- CDN cost.
  - Assume 100% of traffic is served from the United States. The average cost per GB is $0.02.
  - Cost of video streaming: 5 million * 5 videos * 0.3GB * $0.02 = $150,000 per day.

## High Level Design

API Servers (everything else) <- User / Clients -> CDN (Streaming video)

- Client: You can watch YouTube on your computer, mobile phone, and smartTV.
- CDN: Videos are stored in CDN. When you press play, a video is streamed from the CDN.
- API servers: Everything else except video streaming goes through API servers. This includes
  feed recommendation, generating video upload URL, updating metadata database and cache,
  user signup, etc.

Video uploading flow


Video streaming flow
- Flow a: upload the actual video
- Flow b: update the metadata

Video streaming flow
- MPEG–DASH. MPEG stands for “Moving Picture Experts Group” and DASH stands for "Dynamic Adaptive Streaming over HTTP".
- Apple HLS. HLS stands for “HTTP Live Streaming”.
- Microsoft Smooth Streaming.
- Adobe HTTP Dynamic Streaming (HDS).

## Deep Dive

### Video transcoding

### Directed acyclic graph (DAG) model

### Video transcoding architecture

### DAG scheduler

### Resource manager

### Task workers

### Temporary storage

### Encoded video

### System optimizations

- Speed optimization: parallelize video uploading
- Speed optimization: place upload centers close to users
- Speed optimization: parallelism everywhere
- Safety optimization: pre-signed upload URL
- Safety optimization: protect your videos
- Cost-saving optimization

### Error handling

## Others

- Scale the API tier: Because API servers are stateless, it is easy to scale API tier horizontally.
- Scale the database: You can talk about database replication and sharding.
- Live streaming: It refers to the process of how a video is recorded and broadcasted in real time.
- Video takedowns

