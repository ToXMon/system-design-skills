---
name: requirements-clarification
description: Use at the start of any system design task, when requirements are vague, or before estimating capacity. Covers scoping the problem down and the clarifying questions that pin down functional requirements and non-functional targets (scale, latency, availability, consistency, growth).
---

# Requirements Clarification

Vague requirements are the norm. Your first job is to turn them into a bounded problem with measurable targets.

## 1. Narrow the scope first

You can't design a whole platform in one pass. Open by scoping down:

- What is the **primary use case**? (e.g. 1:1 messaging — not group calls, not stories)
- Which features are **in scope / out of scope**? (e.g. group messaging? media types? payments?)
- Who are the users, and how do they reach the system? (mobile app, web, API clients?)

Write the in/out list down. Everything downstream refers back to it.

## 2. Functional requirements

The actions the system must support, stated plainly:

- What can users do? (send messages, upload songs, book rooms, search properties…)
- What are the core reads and writes?

## 3. Non-functional targets

Pin each of these to a number or an explicit stance — "fast" and "scalable" are not targets:

- **Scale:** how many users? Daily actives? Requests per day?
- **Latency:** what must the common case hit? (e.g. the majority of API requests within 100ms)
- **Availability:** how always-on? ("highly available and always operational" vs best-effort)
- **Consistency:** where is strong consistency non-negotiable (money, inventory, bookings) vs where eventual consistency is fine (search, feeds, chat)?
- **Growth:** what does a year from now look like? (e.g. double the user base)
- **Read vs write heavy:** which dominates? This single answer drives half your later choices.

## Clarifying questions to ask

When the request is vague, ask (in plain language):

1. What is the primary use case we're designing for?
2. What's explicitly out of scope?
3. How many users / daily actives should we plan for, and how fast is that growing?
4. Is the workload read-heavy or write-heavy?
5. Do we need real-time updates, or is request/response fine?
6. What latency is acceptable for the common operations?
7. Where would stale or inconsistent data cause real harm (payments, inventory, bookings)?

## Output

End this step with a short requirements brief: in-scope features, the functional list, and each non-functional target with its number. If a target is unknown, record your assumption explicitly — assumptions are allowed, silent ones are not.
