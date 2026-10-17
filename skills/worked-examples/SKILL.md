---
name: worked-examples
description: Use when you want a concrete reference architecture to reason from. Three worked examples from the course — a Spotify-like music streamer, a Booking.com-like hotel reservation system, and a WhatsApp-like messenger — each showing the five-step framework applied end to end.
---

# Worked Examples

Three reference architectures from the course. Use them as worked patterns, not templates to copy — every new problem still starts at step 1 of the framework.

- [spotify.md](spotify.md) — music streaming: blob + SQL metadata, CDN, LRU cache, read replicas. The read-heavy scaling archetype.
- [booking.md](booking.md) — hotel reservations: ACID write path, inventory concurrency control, idempotent APIs, Elasticsearch for search. The strong-consistency archetype.
- [whatsapp.md](whatsapp.md) — messaging: WebSockets, message distributor + queue, NoSQL, persist-before-forward, offline delivery. The real-time write-heavy archetype.

## How to use them

1. Match the new problem to the closest archetype by workload shape (read-heavy? write-heavy? real-time? money/inventory on the write path?).
2. Borrow the *reasoning*, not the diagram: which bottleneck each component answers, and what the estimates said.
3. Note where the course leaves a decision open (marked in each file) — those are exactly the trade-off discussions a senior is expected to lead.
