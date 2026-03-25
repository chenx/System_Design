# Youtube Video Recommendation System Design

https://www.hellointerview.com/learn/ml-system-design/problem-breakdowns/video-recommendations

## Clarifying questions

- What types of recommendations do we need to generate?
- number of videos: 1B
- DAU: 1B
- latency: 250ms

## Business Objective

- Bad: Maximize CTR
- Good: Maximize total watch time
- Good: Maximize quality-adjusted watch time
- Great: Maximize long-term satisfaction and engagement

## ML Objective

- Ranking

## High level design

Candidate generators
- 10k videos
- multiple (could be 100+) generators running in parallel to produce candidate videos

Lignt-weight ranker
- 100 videos
-  computational optimization, using fast-to-compute features to reduce our candidate set from O(10k) videos to O(100).
-  The key here is to optimize for recall:
   -  make sure we don't discard any videos that might end up being great recommendations

Heavy-weight ranker
- 100 videos

Re-ranker
- video slates
- apply our value model to balance user engagement, creator success, and platform health, 
  ensure diversity, and handle special cases like new creator promotion or viral content.

## Data and Features

### Training Data

Explicit Feedback
- Likes and dislikes
- Subscriptions
- "Not interested" feedback

Implicit Feedback
- Watch time (both absolute and relative to video length)
- Click history
- Sharing behavior
- Whether users return to the platform after watching

Contextual Data

    Details about the current video they are watching (e.g. creator, title, semantics, etc.)
    Previous search queries
    Time of day and day of week
    Device type and form factor
    Previous videos watched in the session

### Features

Video Features

Content-based features can be computed once, when the video is uploaded or edited, and cached for a long time.

    Video metadata (title, description, tags)
    Thumbnail features (extracted via computer vision)
    Audio features (music, speech, etc.)
    Video quality metrics (resolution, stability)
    Topic and category embeddings

Engagement features are more dynamic and will need to be updated more frequently.

    Historical engagement metrics (views, likes, average watch time)
    Velocity metrics (recent growth in views/engagement)
    Creator reputation and historical performance
    Monetization status and advertiser friendliness

User Features

1) profile features

    Topic preferences
    Language preferences
    Subscription list embeddings
    Demographics (if available)

2) behavioral features

    Session-based features (recently watched videos, searches)
    Long-term preferences (favorite creators, categories)
    Time-of-day and day-of-week patterns
    Device and platform preferences

## Modeling

### Benchmark Models

### Model Selection

For our ranking models, we have different priorities:

- Light ranker: high recall, low latency, high throughput
- Heavy ranker: high precision, high quality, lower throughput

Light ranker:

Our light ranker is often a tree-based model like a GBDT (gradient boosted decision tree), 
a very skinny MLP (multi-layer perceptron), or a combination of the two.

Heavy ranker:
- Bad: Simple MLP
- Good: Deep Learning Recommendation Model (DLRM)
- Great: Transformer-based Sequence Ranker

## Model Architecture

### Candidate Generation Models

- If we're building a "videos this user will like" generator, we'll assemble a dataset of triplets of the form 
  (user, positive_video, negative_video). 
- Two parallel towers will be trained to produce embeddings for the user and the candidate videos.
- The loss function will then be:
```
L = max(0, margin + d(user, positive_video) - d(user, negative_video))
```

### Heavy Ranking Model

- Input Processing:

        Embedding layers for categorical features (video_id, creator_id, etc.)
        Normalization layers for numerical features (view counts, engagement rates)
        Sequence processing for historical features with positional encoding
        Special tokens to denote different types of user actions (watch, like, share)

- Feature Interaction:

        Cross-attention layers to model interactions between user history and candidate videos
        Self-attention layers to capture patterns within user history
        Feed-forward networks to process the attended information
        Residual connections and layer normalization for stable training

- Output Layers:

Multiple classification and regression heads, each specialized for different prediction tasks:

        Watch time prediction (regression)
        Click-through probability (binary classification)
        Like probability (binary classification)
        Share probability (binary classification)
        Completion rate (regression)
        Return visit probability (binary classification)

## Inference and Evaluation

### Inference System

### Evaluation Framework

#### Online Evaluation

    A/B Testing Metrics:

        Session watch time
        Return visit rate
        Long-term engagement trends
        Creator satisfaction metrics

    User Experience Metrics:

        Recommendation acceptance rate
        Survey feedback
        Negative feedback rate

    System Health Metrics:

        Latency
        Throughput
        Resource utilization
        Error rate

## Deep Dives

### Feedback Loops

### Cold Starting Users/Videos

### Explore/Exploit Tradeoffs

## What is Expected at Each Level?

For this problem, mid-level engineers are expected to 
- demonstrate practical proficiency with recommendation systems and their core components. 
- articulate the basic architecture of a recommendation system, including candidate generation and ranking stages.
- show familiarity with common engagement signals (likes, watch time, CTR) and how to incorporate them as features.
- understand standard evaluation metrics like NDCG and MAP,
- and demonstrate awareness of the scaling challenges inherent in serving recommendations to billions of users.
- can implement a working recommendation system using established patterns and best practices.

Senior-level engineers 
- demonstrate significantly more depth in their understanding of recommendation systems.
- Their expertise in feature engineering should be apparent in how they handle data quality issues, normalize engagement signals, and address temporal aspects of the data.
- have experience with multi-stage architectures and be able to articulate the tradeoffs between different approaches.
- demonstrate strong knowledge of serving optimizations like caching strategies, embedding compression, and efficient nearest neighbor search.
- can balance competing objectives - understanding how to trade off metrics like engagement, diversity, and creator success.
- bring up practical considerations around A/B testing, monitoring, and maintaining recommendation quality over time.

Staff-level candidates 
- demonstrate mastery of recommendation systems at both technical and strategic levels.
- They should quickly establish the core architecture and then focus on the most impactful and challenging aspects of the system.
- identify and propose solutions to systemic issues like feedback loops (popular content getting more recommendations and thus more engagement), cold-start problems (how to recommend new content or serve new users), and data quality challenges (dealing with position bias, missing data, and noise in user feedback).
- have a deep understanding of how recommendation biases can affect both user experience and creator ecosystems, and will propose creative solutions to measure and mitigate these effects.
- usually recognize that the bottleneck in modern recommendation systems isn't just model architecture and that it's often about data quality, evaluation methodology, and overall system design.

