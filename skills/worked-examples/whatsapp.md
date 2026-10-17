# Worked Example: WhatsApp-like Messenger

The course's real-time write-heavy archetype. Design target: **10 billion messages/day**.

> Provenance note: the core flow and estimates come from the creator's companion write-up of the same design; the expanded deep-dive topics (fanout, multi-device sync, encryption, rate limiting, sharding, cleanup, failure handling, feature flags) are confirmed by the course's chapter titles, but their detailed choices are not in public text and are marked below.

## Capacity estimates

- 10B messages/day ÷ 86,400 ≈ **115,740 messages/sec** average
- Doubling within a year → plan for ~**231,480 MPS**
- 200 bytes/message → **2TB/day** → ~**1.5PB/year**
- Plan for peaks, not averages. 1.5PB/year means one database node is not an option — sharding is decided here, not later.

## Architecture

**Core flow:** client → load balancer → stateless API servers → **Message Distributor** → queue (Kafka/RabbitMQ) → workers → **NoSQL** (Cassandra/DynamoDB).

**Persist-before-forward:** the message is written to the store *first*, then delivery is attempted — so a crash mid-push never loses a message.

**Real-time push:** persistent **WebSocket** connections from clients to chat servers; the server pushes down the recipient's socket; the client ACKs. Message status walks sent → delivered → read. Server-assigned IDs/timestamps are the ordering source of truth.

## API & data model

- REST endpoints: `POST /messages`, `GET /messages` (unread pull), `GET /messages/:id`, `PUT/PATCH /messages/:id` (read receipts).
- Per-device inboxes with delivery offsets; shard by userId; locate shards via consistent-hashing ring or metadata service.
- NoSQL fits: extreme write volume, eventual consistency acceptable. (Cassandra for write throughput/scale; DynamoDB for minimal operations.)

## Deep-dive topics (course chapters; details not in public text)

- **Offline delivery:** message already persisted in the recipient's inbox/queue; push notification (APNs/FCM) wakes the device; on reconnect the client pulls everything missed since its last-seen message ID, in order, and ACKs. Delivery deferred, never dropped.
- **Multi-device sync:** conversation state and delivery offsets synced across a user's devices.
- **Fanout:** group messaging fans out one message to N recipients server-side — the classic fan-out-on-write vs fan-out-on-read trade-off. *The course's chosen strategy is not in public text.*
- **Media + CDN:** images/video in blob storage with compression and multiple thumbnail sizes, delivered via CDN; text stays in NoSQL.
- **Event-driven + batch:** Kafka-style backbone for async work (notifications, analytics, read-receipt aggregation); batch processing for non-real-time jobs.
- **Encryption + rate limiting:** end-to-end encryption as a design discussion; per-user/per-device rate limiting against abuse and spam. *Specific algorithms not in public text.*
- **Sharding + cleanup:** horizontal sharding of message data; TTL/cleanup jobs to expire old messages and bound storage.
- **Failure handling + global scaling:** multi-region deployment, replication/quorum, failover and recovery; feature flags and staged rollouts for safe launches.

## The pattern to reuse

Persist before forward; separate the real-time push plane (WebSockets) from the durable plane (queue + NoSQL); design offline delivery as the normal case, not the edge case; let the write volume pick the database.
