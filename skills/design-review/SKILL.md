---
name: design-review
description: Use at the end of any system design session to self-review the architecture. Applies the course's five evaluation dimensions — communication, structured thinking, technical judgment, practical depth, time management — as a checklist before calling a design done.
---

# Design Review

Before a design is done, review it against the five dimensions the course says designs are actually judged on. (Wording via a course-specific secondary source; the course itself confirms interviewers score five things, most of which aren't the diagram.)

## The five dimensions

### 1. Communication & collaboration
- Did you ask clarifying questions before designing, and validate assumptions out loud?
- Could another engineer follow your reasoning from the doc alone?
- Are scope boundaries (in/out) stated explicitly?

### 2. Structured thinking
- Did you run the five steps in order, or jump to components?
- Does the design progress logically: requirements → estimates → simple architecture → APIs/data → scaling?
- Is there exactly one bottleneck addressed per scaling iteration?

### 3. Technical judgment
- For every database, cache, queue, and protocol choice: is the *why* written down?
- Is each choice tied to a requirement, an estimate, or a named bottleneck — not to popularity?
- Does each choice carry its cost and what would change it? (See `tradeoff-analysis`.)

### 4. Practical depth
- Does this read like something that could actually be built and operated, or like theory?
- Are failure modes named with detection and degradation behavior? (See `scaling-bottlenecks`.)
- Are the hard parts (concurrency on inventory, offline delivery, fanout) addressed concretely rather than hand-waved?

### 5. Time management
- Was effort spent proportional to importance — requirements and bottlenecks, not gold-plating the easy parts?
- If this were a 60-minute exercise, would the split hold: ~5–10 min requirements, ~15–20 min data/API + ~15–20 min architecture (order flexible), ~5–10 min bottlenecks/scale/reliability, then trade-off wrap-up?

## Sign-off

A design passes review when every dimension gets a yes, or the gaps are written down as known risks with owners. File the completed `templates/design-doc.md` as the record.
