# System Design Doc: <System Name>

_Work through top to bottom. Each section maps to a step of the framework._

## 1. Requirements

**Scope:** in-scope / out-of-scope (explicit list).

**Functional requirements:**
- …

**Non-functional targets:**

| Target | Value | Notes / assumption |
|---|---|---|
| Users / DAU | | |
| Requests per day | | |
| Latency (common case) | | |
| Availability | | |
| Consistency (per write path) | | |
| Growth (1 year) | | |
| Read-heavy or write-heavy | | |

## 2. Capacity estimation

| Quantity | Assumption | Math | Result |
|---|---|---|---|
| Avg requests/sec | | daily ÷ 86,400 | |
| Peak requests/sec | | | plan for peaks, not averages |
| Bytes per unit | | | |
| Storage (per entity) | | bytes × units | |
| Storage with growth | | | |
| Hot working set | | fits one cache cluster? | |
| Peak connections | | ÷ connections/server | |

## 3. High-level architecture

Start: client → load balancer → stateless servers → database + blob storage.

- **Read path:** …
- **Write path:** …

## 4. API design & data modeling

**Endpoints:**

| Method & path | Purpose | Idempotent? |
|---|---|---|
| | | |

**Entities & storage:**

| Entity | Store (SQL / NoSQL / blob) | Why this store | Consistency requirement |
|---|---|---|---|
| | | | |

## 5. Scaling & bottlenecks

| Load level | Bottleneck | Fix | Cost of the fix |
|---|---|---|---|
| | | | |

## 6. Trade-offs

| Choice | Requirement driving it | Cost | What would change it |
|---|---|---|---|
| | | | |

## 7. Failure modes

| What breaks | How we detect it | How the system degrades |
|---|---|---|
| | | |

## 8. Review

Run the `design-review` rubric: communication, structured thinking, technical judgment, practical depth, time management. Record gaps as known risks with owners.
