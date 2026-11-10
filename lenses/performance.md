---
name: performance
applies-when: the change adds queries, loops over collections, network calls, rendering of lists, or runs on a hot path or a schedule
---

# Performance

## Hunt for
- N+1 queries or calls inside loops
- unbounded reads (no pagination, no limit) on data that grows
- missing index for a new query pattern
- work repeated on every request that could be done once
- large payloads sent to the client, or re-renders on every keystroke
- jobs whose run time grows with total data instead of new data

## Not this lens
- micro-optimizations with no measurable effect at the project's scale
