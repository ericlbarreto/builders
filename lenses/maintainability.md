---
name: maintainability
applies-when: the change adds new modules, abstractions, or more than a few hundred lines
---

# Maintainability

## Hunt for
- logic duplicated from an existing helper instead of reusing it
- an abstraction with one caller and no second one in sight
- a new pattern where the project already has an established one for the same job
- dead code, unused flags, commented-out blocks left behind
- names that say what the code does not do

## Not this lens
- formatting (the project's formatter owns it)
- personal style preferences
