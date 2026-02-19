# Scalable News Feed System Design

## Overview

This project presents the system design of a scalable News Feed architecture similar to Facebook or Instagram. The system is designed to handle millions of users creating posts and viewing personalized feeds with low latency and high availability.

---

## Problem Statement

Design a distributed News Feed system that:

- Allows users to create posts
- Allows users to follow other users
- Generates a personalized news feed
- Supports real-time updates
- Scales to millions of daily active users
- Maintains low latency for feed reads

---

## High-Level Architecture

The architecture consists of:

- DNS
- Load Balancer
- Web Servers
- Authentication and Rate Limiting
- Post Service
- Fanout Service
- Message Queue
- Caching Layer
- Graph Database
- User Database
- Post Database
- Notification Service

The system primarily uses a fanout-on-write strategy to precompute feeds for faster read performance.

---

## System Flow

### 1. User Request

Users access the system via Web Browser or Mobile App. DNS resolves the domain and routes traffic to the Load Balancer.

### 2. Load Balancer

Distributes incoming traffic across multiple Web Servers.

### 3. Web Servers

Responsible for:
- Authentication
- Rate limiting
- API routing
- Request validation

### 4. Post Creation

- User creates a post.
- Post Service stores the post in Post DB.
- Post Cache is updated.

### 5. Fanout Process

- Fanout Service fetches friend IDs from Graph DB.
- Publishes tasks to the Message Queue.
- Asynchronously updates the News Feed Cache for followers.

### 6. Feed Retrieval

- When users request their feed, data is served from News Feed Cache.
- Falls back to the database in case of a cache miss.

---

## Database Design

### User Database

Stores:
- User profile
- Account details
- Preferences

### Post Database

Stores:
- Post ID
- User ID
- Content
- Timestamp

### Graph Database

Stores:
- Follower and following relationships

---

## Caching Strategy

Multiple caching layers are used:

- Post Cache to reduce database load for posts
- User Cache to speed up profile access
- News Feed Cache to store precomputed feeds

An in-memory store such as Redis can be used for caching.

---

## Message Queue

Used for asynchronous processing to:

- Prevent overload on web servers
- Handle large fanout tasks
- Improve reliability
- Enable retry mechanisms

Examples include Kafka or RabbitMQ.

---

## Notification Service

Triggers notifications when:

- A new post is created
- A user interaction occurs

---

## Capacity Estimation (Example Assumptions)

- 10 million daily active users
- 500 posts per second
- 5,000 feed reads per second
- Average post size: 5 KB
- Estimated storage per day: approximately 200–300 GB

These numbers can scale horizontally using:
- Database sharding
- Caching
- Load balancing

---

## Design Decisions

### Fanout on Write

Feeds are precomputed when a post is created.

Advantages:
- Faster read performance

Disadvantages:
- Higher write amplification

### Asynchronous Processing

Message queues provide:
- Better fault tolerance
- Scalable background processing

### Caching First Approach

Most read requests are served from cache to minimize latency.

---

## Scalability Strategy

- Horizontal scaling of web servers
- Database sharding by User ID
- Read replicas for Post DB
- Redis Cluster for distributed caching
- Partitioned message queues

---

## Potential Bottlenecks

- Celebrity accounts with millions of followers
- Cache invalidation complexity
- Large-scale fanout operations

Hybrid fanout strategies can be implemented to handle these cases.

---

## Possible Improvements

- Add CDN for media content
- Implement feed ranking algorithm
- Add monitoring using Prometheus and Grafana
- Add circuit breakers for fault isolation
- Use API Gateway
- Implement distributed tracing

---

## Future Enhancements

- Machine learning-based feed ranking
- Real-time engagement tracking
- A/B testing for feed optimization
- Analytics pipeline integration

---

## Conclusion

This design focuses on scalability, low latency, high availability, asynchronous processing, and distributed caching. The architecture demonstrates how large-scale social media platforms manage personalized feeds efficiently.
