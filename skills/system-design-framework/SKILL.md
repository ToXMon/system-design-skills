---
name: system-design-framework
description: Use when asked to design a software system, architect a backend, or answer a system design question. Provides the senior-engineer five-step framework: clarify requirements, estimate capacity, high-level architecture, API + data modeling, then scaling, bottleneck, and trade-off deep dives.
---

# System Design Framework

One repeatable framework, applied from a blank page to millions of users. Don't memorize architectures — run the steps, in order, every time.

## The five steps

> Note: the course states the five steps in the video itself; no public text gives their exact wording. This reconstruction follows the course's chapter structure and the creator's companion material, and matches the skeleton all three worked examples use.

### 1. Clarify requirements
Pin down what you're building before designing anything. Separate functional requirements (what the system does) from non-functional targets (scale, latency, availability, consistency, growth). Narrow scope explicitly — designing an entire platform in one pass is unrealistic, so state what's in and what's out. Ask questions; don't assume.
See the `requirements-clarification` skill.

### 2. Capacity estimation
Do back-of-the-envelope math *before* drawing boxes: per-second request rates, storage needs, growth projections. Plan for peaks, not averages. The numbers you produce here justify every component you add later.
See the `capacity-estimation` skill.

### 3. High-level architecture & data flows
Start with the simplest end-to-end design that works: client → load balancer → stateless servers → database + blob storage. Draw data flows, not just boxes — show what travels where on a read path and a write path. Resist adding components until step 5 demands them.

### 4. API design + data modeling
Define the contracts (endpoints, request/response shapes) and the entities they operate on. Decide storage per entity: SQL for structured, relational data with complex queries; NoSQL when write volume is extreme and eventual consistency is acceptable. Make mutating APIs idempotent.
See the `api-data-modeling` skill.

### 5. Deep dive: scaling, bottlenecks, trade-offs
Ask "what breaks first as load grows?" Fix it, then ask again. Each iteration names one bottleneck and the change that removes it: replication for read-heavy load, sharding when one database can't hold the data, caching/CDN for repeated reads, queues to decouple bursty writes. For every significant choice, state the decision, the requirement driving it, its cost, and what would change your mind.
See the `scaling-bottlenecks` and `tradeoff-analysis` skills.

## How the course applies it

Each worked example in `worked-examples` runs this exact sequence:
- **Spotify-like streamer:** requirements + capacity → simple architecture (app → LB → web servers → SQL + blob) → API/schema design → scale to 50M users via CDN, LRU cache, read replicas.
- **Booking.com-like reservations:** non-functional targets (strong consistency on the booking write path) → capacity math → idempotent APIs + inventory data model → concurrency control → sharding, caching, Elasticsearch for search, global routing.
- **WhatsApp-like messenger:** scale estimation (10B messages/day) → real-time architecture (WebSockets, message distributor, queue, NoSQL) → API flows + data model → offline delivery, fanout, media/CDN, encryption, rate limiting, sharding, failure handling.

## Rules

- Never skip or reorder steps. Estimation before components; APIs before scaling debates.
- A component added without a bottleneck or a number justifying it gets removed.
- End every design with the `design-review` rubric.
