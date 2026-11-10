---
name: tests
applies-when: always, except docs-only changes
---

# Tests

## Hunt for
- new behavior with no test that would fail if it broke
- tests that assert implementation details or mocks instead of observable results
- missing negative cases: wrong permission, invalid input, empty state, the boundary another owner or tenant must not cross
- tests that pass for the wrong reason (always-true assertions, swallowed errors, skipped tests)
- flaky constructs: real time, real randomness, ordering assumptions, shared state between tests

- bloat: many near-duplicate tests for one behavior, tests of trivial wiring, or snapshot dumps that hide what is asserted

## Not this lens
- coverage percentages for their own sake
- asking for more tests when one per behavior plus its key negative case already exists
