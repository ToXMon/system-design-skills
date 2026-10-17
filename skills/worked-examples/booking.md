# Worked Example: Booking.com-like Hotel Reservations

The course's strong-consistency archetype. The defining trait: the booking write path touches inventory and money, so inconsistency causes double bookings and refunds.

> Provenance note: the course covers this system across a full chapter sequence, but no public text gives its complete internals. What's below is reconstructed from the chapter titles plus secondary sources, and marked where inferred.

## Non-functional targets (from the course)

- High availability for **search/discovery** (eventual consistency acceptable).
- **Strong consistency on the booking write path** — two reservations for the same room on the same dates must be impossible.
- Dynamic pricing is a feature area.

## Architecture (reconstructed)

- **SQL database** for bookings, inventory, and payments (ACID transactions on the write path).
- **Inventory as the contended resource:** properties → room types → availability per date.
- **Elasticsearch** for property search at scale (full-text, filters, geo), updated from the DB via CDC; the SQL store stays the source of truth.
- **Redis** caching hot search data.
- **Message queue** (Kafka-style) for booking events and notifications, decoupling the API from downstream work.

## API design

- Reservation APIs are **idempotent** (idempotency keys on create-booking): user double-clicks and network retries must not create duplicate bookings. *Inferred from the chapter "API Design and Idempotency" — standard pattern.*

## Concurrency: the double-booking problem

A naive select-then-insert fails when two users take the last room simultaneously. The course's fix: strong consistency on the write path via transaction isolation, optimistic/pessimistic locking, or distributed locks (Redis). For multi-step flows (reserve → pay), the **Saga pattern** avoids holding inventory locks while payment processes. *Mechanics inferred from secondary sources; the course's chapter "Concurrency and Race Conditions" confirms the topic.*

## Scaling (from chapter sequence)

- **Shard** booking data (e.g. by hotel/region) to keep a hotel's transactions within one shard.
- **Cache** hot search data in Redis; **Elasticsearch** for search at scale.
- **Read replicas + global routing** for multi-region serving.

## The pattern to reuse

Find the contended resource on day one; give its write path strong consistency and idempotent APIs; let everything else (search, recommendations, analytics) be eventually consistent and independently scalable.
