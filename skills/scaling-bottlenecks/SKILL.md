---
name: scaling-bottlenecks
description: Use when a simple architecture needs to grow — or when asked what breaks at scale. Teaches bottleneck-driven iteration: start from the simplest end-to-end design, find what breaks first as load grows, fix it, repeat. Covers replication, sharding, caching, CDN, queues, and failure handling.
---

# Scaling & Bottlenecks

Start with the simplest architecture that works end-to-end, then grow it one bottleneck at a time. Each scaling step must name the bottleneck it removes.

## The starting point

The course's canonical simple design: **client → load balancer → stateless web servers → SQL database + blob storage.** It handles the small case completely. Everything below is added only when load demands it.

## The iteration loop

1. Scale the load 10–100x in your head (or redo the capacity math).
2. Name the **single component that breaks first**.
3. Apply the standard fix for that bottleneck.
4. Repeat.

## Bottleneck → fix catalog

- **One server can't take the traffic** → load balancer in front; keep app servers stateless so they scale horizontally. The LB also removes the single point of failure.
- **Reads overwhelm the database** (many listeners, few uploaders) → **Leader→Follower replication**: one read/write leader, multiple read-only followers.
- **One database can't hold the data** (e.g. 1.5PB of messages) → **shard**, e.g. by userId. Locate shards with a consistent-hashing ring or a metadata service. Keep a single entity's transactions within one shard where possible (e.g. shard bookings by hotel/region).
- **Repeated reads of the same data** → **cache**: Redis for hot reads (search results, active documents); LRU eviction for popular items; fetch misses from origin, then cache them.
- **Static/large content served globally** → **CDN per region** (CloudFront/Cloudflare): popular songs, images, video served close to users; media uploads get compression + multiple thumbnail sizes.
- **Bursty writes overwhelm the database** → **queue** (Kafka/RabbitMQ): accept the request, enqueue, ack immediately, let workers persist asynchronously. The course's WhatsApp flow: API servers → Message Distributor → queue → workers → NoSQL, with **persist-before-forward** so a mid-push crash never loses a message.
- **Slow full-text/filtered search** → dedicated **search index** (Elasticsearch), updated from the database via CDC; the transactional store stays the source of truth.
- **Regional failure / global users** → multi-region deployment: read replicas + global routing, replication/quorum, failover plans, staged rollouts behind feature flags.

## Failure handling

For each critical path, name: what breaks, how you detect it, how the system degrades. Examples from the course's WhatsApp deep dive: offline delivery via persisted inbox + push notification + pull-by-last-seen-ID on reconnect (delivery deferred, never dropped); TTL/cleanup jobs to bound storage; rate limiting per user/device against abuse and spam.

## Rules

- Never add two scaling mechanisms at once. One bottleneck, one fix, then re-evaluate.
- Every fix states its cost: sharding complicates queries, caching introduces staleness, queues add latency and operational surface.
- If the capacity math doesn't show the bottleneck, you don't have one yet — stop scaling.
