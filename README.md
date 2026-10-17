# System Design Skills

Teach your AI coding models to architect software systems like a senior engineer.

This repo distills the lessons of freeCodeCamp's **"Design Software Systems Like a Senior Engineer"** course (3h16m, published Oct 2026 — a re-upload of Hayk Simonyan's full system-design course) into reusable **skills** and **agent instructions**. The course's core idea: don't memorize architectures. Learn one repeatable framework and apply it from a blank page to millions of users, grounding every choice in requirements, estimates, and explicit trade-offs. It demonstrates the framework on three worked examples: a Spotify-like music streamer, a Booking.com-like hotel reservation platform, and a WhatsApp-like messenger.

## Contents

- `AGENTS.md` — agent instructions: the senior-architect persona and the operating rules every design session follows.
- `skills/` — one skill per design capability, each a `SKILL.md` in the standard skill format (YAML frontmatter + guidance):
  - `system-design-framework` — the five-step framework: clarify → estimate → high-level architecture → APIs + data model → scale/bottlenecks/trade-offs.
  - `requirements-clarification` — scoping and clarifying questions; functional vs non-functional targets.
  - `capacity-estimation` — back-of-the-envelope storage/traffic math with worked numbers.
  - `api-data-modeling` — API contracts, idempotency, schema design, SQL vs NoSQL.
  - `scaling-bottlenecks` — start simple, then iterate on bottlenecks: replication, sharding, caching, CDN, queues.
  - `tradeoff-analysis` — the decision format (choice → requirement → cost → what would change it) plus a catalog of classic trade-offs.
  - `design-review` — the five evaluation dimensions as a self-review rubric.
  - `worked-examples` — reference architectures for the Spotify, Booking.com, and WhatsApp designs.
- `templates/` — a system design doc template and an estimation worksheet.
- `SOURCES.md` — every source used, and what could not be verified.

## How to use

- **Claude Code / compatible agents:** copy `skills/*` into your skills directory (e.g. `~/.claude/skills/`), or point your agent at this repo. Each skill's frontmatter `description` tells the model when to invoke it.
- **Any coding model:** paste `AGENTS.md` as system instructions (most harnesses pick up a repo-root `AGENTS.md` automatically), and attach the relevant skill files as context for the design task at hand.
- **Design sessions:** have the model work through `templates/design-doc.md` top to bottom — it walks the five steps in order.

## Provenance & confidence

Distilled Oct 2026 from the course's video description + chapter timestamps, the freeCodeCamp announcement article, a course-specific daily.dev Q&A, and the creator's companion write-ups of the same examples. A few things could not be verified from public text and are marked where they appear:

- The exact wording of the five framework steps (spoken only in the video) — reconstructed from the course's chapter structure and the creator's companion material.
- Booking.com example internals — reconstructed from chapter titles plus secondary sources.
- Some WhatsApp deep-dive specifics (fanout strategy, sharding key, rate-limit algorithm) — topics confirmed, detailed choices not in public text.
- The five evaluation dimensions — from a course-specific daily.dev page (secondary source).

Pairs well with an AI-systems spec (e.g. data/eval/deployment guidance) for designs where the system being architected *is* an AI product.
