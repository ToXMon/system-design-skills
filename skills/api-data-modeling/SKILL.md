---
name: api-data-modeling
description: Use when defining service contracts and storage for a system design. Covers REST API design, idempotent mutating endpoints, entity/schema modeling, and choosing SQL vs NoSQL by access patterns and consistency needs.
---

# API Design & Data Modeling

Contracts first, storage per entity. The API is what clients depend on; the schema is what you have to live with.

## API design

- Define endpoints with request/response shapes for each core flow (e.g. `POST /messages`, `GET /messages` for unread pull, `PUT /messages/:id` for read receipts).
- **Make mutating APIs idempotent.** Retries happen — user double-clicks, networks retry, queues redeliver. An idempotency key on create-booking / create-order style calls means a retry can't create a duplicate. The course's Booking.com example treats this as non-optional.
- Prefer stateless servers behind the API: any server can handle any request, which is what lets you scale horizontally behind a load balancer.

## Data modeling

- Model the core entities and their relationships (e.g. Users, Songs, Artists + an ArtistsSongs join; or properties, room types, inventory, bookings, payments).
- Separate **metadata from blobs**: small structured rows (song metadata, user records) live in the database; large files (audio, images, video) live in blob storage (S3/GCS/Azure), with the database storing a file URL reference.
- Name the contended resource explicitly (e.g. room inventory for specific dates) — that's where your concurrency design goes.

## SQL vs NoSQL

Decide by **access patterns + consistency needs**, not popularity:

- **SQL** (Postgres/MySQL) when data is structured and relational, needs complex queries and joins, and the write path demands strong consistency: user profiles, song/artist metadata, bookings and payments.
- **NoSQL** (Cassandra/DynamoDB) when write volume is extreme and eventual consistency is acceptable: 10B chat messages/day. Cassandra is favored for write throughput and scalability; DynamoDB when you want minimal operational maintenance.

## Consistency placement

- **Strong consistency on write paths that touch money or inventory.** A naive select-then-insert lets two users grab the last hotel room simultaneously → double booking → refunds. Fix with transaction isolation, optimistic/pessimistic locking, distributed locks (Redis), or the Saga pattern for multi-step flows like payment-after-reservation.
- **Eventual consistency is fine** for search/discovery, feeds, and chat delivery — availability and write throughput win there.

## Rules

- Every entity gets: what it stores, why this store type, and its consistency requirement.
- If two entities have different access patterns, they can live in different stores. One database for everything is a starting point, not a law.
