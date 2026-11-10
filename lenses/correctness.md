---
name: correctness
applies-when: always
---

# Correctness

## Hunt for
- logic that does not do what the change claims, on some input
- off-by-one, wrong comparison, inverted condition, wrong default
- unhandled empty, null, zero, negative or very large inputs
- error paths that swallow failures or report success
- async mistakes: missing await, unhandled rejection, race between reads and writes
- callers or second implementations that were not updated with the change

## Not this lens
- naming, formatting, structure (maintainability)
- slowness (performance)
