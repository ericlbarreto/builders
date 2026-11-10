# Lenses

A lens is one point of view: what a single reviewer hunts for. Built-in lenses here hold for any project. Projects add their own in `.flow/lenses/<name>.md`, and triage derives new ones on the fly.

## Format

Every lens, built-in, project or derived, uses this shape so any module can run it:

```markdown
---
name: security
applies-when: the change handles input, auth, secrets, files, network calls or permissions
---

# Security

## Hunt for
- concrete defect classes, one per bullet

## Not this lens
- what to leave to other lenses

## Rules
- project-specific must/never rules (mostly in project lenses)
```

`applies-when` is what triage reads to decide whether to run the lens. Keep lenses short: a lens that tries to cover everything finds nothing.
