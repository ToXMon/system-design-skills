---
name: tradeoff-analysis
description: Use when choosing between technologies or architectures in a system design. Provides the course's decision format (choice → requirement → cost → what would change it) and a catalog of classic trade-offs: WebSockets vs HTTP, load balancers, SQL vs NoSQL, caching/CDN, consistency vs availability, queues, replication, sharding.
---

# Trade-off Analysis

Senior engineers don't pick technologies — they pick trade-offs and can defend them. Every significant choice in a design gets this treatment.

## The decision format

State each choice as:

1. **Choice:** what you picked (e.g. WebSockets for client updates).
2. **Requirement:** the requirement driving it (e.g. server must push messages in real time).
3. **Cost:** what you pay for it (e.g. long-lived connections consume server resources; harder to scale horizontally than stateless HTTP).
4. **What would change it:** the requirement change that flips the decision (e.g. if updates only needed polling every 30s, plain HTTP wins).

If you can't fill in all four, you don't understand the choice well enough yet.

## Catalog of classic trade-offs (from the course)

- **WebSockets vs HTTP.** HTTP is stateless request/response — simple, scales horizontally, but the server can't push. WebSockets give a full-duplex channel over one long-lived connection so the server can push updates. Choose by real-time need: chat and live feeds need push (polling wastes); anything else, HTTP's simplicity wins.
- **Adding a load balancer.** Cost: another component to operate. Buys: traffic distribution, availability, fault tolerance, no single point of failure — and it lets the app servers behind it stay stateless and scale horizontally.
- **SQL vs NoSQL.** SQL: structured relational data, complex queries/joins, strong consistency (users, metadata, bookings). NoSQL (Cassandra/DynamoDB): extreme write volume where eventual consistency is acceptable (10B messages/day). Decide by access patterns + consistency needs, never by hype.
- **Caching / CDN.** Cost: staleness, invalidation complexity, another system to run. Buys: absorbing read-heavy load and serving repeated reads fast. Add only when scale demands it: CDN per region for popular content, LRU for hot items, Redis for hot reads.
- **Consistency vs availability.** Strong consistency on write paths where inconsistency causes real harm (double-booked rooms, double-charged payments) — even though it costs throughput and complexity. Eventual consistency where staleness is harmless (search results, feeds, chat delivery) — availability and write throughput win.
- **Queues (Kafka/RabbitMQ).** Cost: latency, operational surface, ordering/duplication semantics to manage. Buys: decoupling bursty intake from steady processing — accept, enqueue, ack fast; workers persist at their own pace.
- **Replication (Leader→Follower).** Cost: replication lag, failover complexity. Buys: read scaling for read-heavy workloads with a single writer.
- **Sharding.** Cost: cross-shard queries get hard, rebalancing is painful, transactions can't span shards easily. Buys: surviving data volumes no single node can hold. Shard by the key your critical transactions filter on (userId for messages, hotel/region for bookings).

## Rules

- Name the loser, not just the winner: "we chose X over Y because…".
- Revisit trade-offs in step 5 of the framework — a choice that's right at 500k users can be wrong at 50M.
- "It depends" is a dodge unless followed by "on X, and here's how we'd know."
