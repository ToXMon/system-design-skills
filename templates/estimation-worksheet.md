# Estimation Worksheet

Back-of-the-envelope math: order of magnitude, not accounting. Show every assumption.

## Method reminders

- Per-second rate = daily volume ÷ 86,400
- Storage = bytes per unit × total units (do it per entity)
- Project growth forward (e.g. doubling in a year)
- **Plan for peaks, not averages**
- Heuristics: ~1% of users active at peak; check the hot set fits one cache cluster; connections ÷ per-server capacity ≈ servers + headroom

## Worksheet

| # | Quantity | Assumption | Math | Result |
|---|---|---|---|---|
| 1 | Users / DAU | | | |
| 2 | Requests per day | | | |
| 3 | Avg requests/sec | | ÷ 86,400 | |
| 4 | Peak requests/sec | | | |
| 5 | Read:write ratio | | | |
| 6 | Entity A: bytes per unit | | | |
| 7 | Entity A: total units | | | |
| 8 | Entity A: total storage | | 6 × 7 | |
| 9 | Entity B: bytes per unit | | | |
| 10 | Entity B: total units | | | |
| 11 | Entity B: total storage | | 9 × 10 | |
| 12 | Growth projection | | | |
| 13 | Hot working set | | fits cache? | |
| 14 | Peak connections | | ÷ per server | |

## What the numbers decide

- Per-second rate → API fleet size, queue throughput, connection counts
- Total storage → one database or sharding (1.5PB won't live on one node)
- Hot-set size → whether a cache helps and how big
- Metadata-vs-blob split → separate SQL from blob storage early
