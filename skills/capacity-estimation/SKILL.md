---
name: capacity-estimation
description: Use after requirements are clarified and before drawing architecture. Teaches back-of-the-envelope storage and traffic estimation: per-second rates, bytes-per-unit math, growth projection, and planning for peaks. Includes worked numbers from the course's Spotify and WhatsApp examples.
---

# Capacity Estimation

Do the math before drawing boxes. Estimates turn "we need a cache" from a vibe into a justified decision.

## The method

1. **Per-second rates:** daily volume ÷ 86,400 → average requests/second.
2. **Storage:** bytes per unit × total units → total storage. Do it per entity (messages, songs, metadata, user records).
3. **Growth:** project forward (e.g. doubling in a year) and size for the future, not today.
4. **Plan for peaks, not averages.** Size for peak traffic; averages hide the spikes that take systems down.
5. **Sanity heuristics:** assume ~1% of users active at peak; check whether the hot working set fits in a single cache cluster; do connections math (peak connections ÷ connections per server ≈ servers needed, plus headroom/autoscaling).

Use the worksheet in `templates/estimation-worksheet.md`.

## Worked examples (from the course)

**Spotify-like streamer** (starts at 500k users / 30M songs):
- Avg song 3MB → 3MB × 30M songs = **90TB** of audio storage
- Song metadata 100 bytes/song → **3GB**
- User metadata 1KB/user → **0.5GB**
- Scaled to 50M users / 200M songs: song metadata 100B × 200M = **20GB**; user metadata 1KB × 50M = **50GB**

**WhatsApp-like messenger** (design target: 10B messages/day):
- 10B ÷ 86,400 ≈ **115,740 messages/sec** average
- Doubling within a year → plan for ~**231,480 MPS**
- 200 bytes/message → **2TB/day** → ~**1.5PB/year**

## How to use the numbers

- The per-second rate sizes your API fleet, queue throughput, and connection counts.
- Total storage tells you whether one database can hold it or you must shard (1.5PB of messages won't live on one node).
- The hot-set size tells you whether a cache helps and how big it must be.
- Metadata-vs-blob split (small structured rows vs large files) tells you to separate the SQL database from blob storage early.

## Rules

- Show every assumption (bytes per unit, daily volume, growth rate). Estimates with hidden assumptions are fiction.
- Round freely — this is order-of-magnitude math, not accounting.
- Revisit the estimates in step 5: when you scale the design 100x, redo the math and check which component breaks first.
