---
name: plan
description: builders plan module. Slice a spec into implementation slices that own disjoint files and form a dependency graph, so independent slices can be built in parallel. Short and actionable, not an essay. Use when the user asks for a builders plan, runs /builders:plan, or has a spec ready to build.
---

# plan

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Read** `spec.md` (required: without a spec, run `spec` first or ask the user for the acceptance criteria), plus `triage.md`, `research.md` and `decision.md` if present. Load context per `reference/context.md`.
2. **Map**: reuse the explorer output from triage when present; otherwise launch one **explorer** (`roles/explorer.md`).
3. **Find shared files** first: schemas, migrations, shared types, routers, configs. These decide the split.
4. **Slice**:
   - A slice is a unit one agent can build and verify alone: it owns a set of files and meets one or more acceptance criteria.
   - **No two slices own the same file.** Shared files go into a `foundation` slice that runs first, or to the one slice that needs them most.
   - Prefer vertical slices (one behavior end to end) when files allow; layer slices (backend, frontend) when they do not.
   - Aim for 2 to 5 slices. One slice is fine for small work.
5. **Order**: list each slice's dependencies. Slices with no unmet dependency form a **wave** and run in parallel.
6. **Write `plan.md`**:

```markdown
## Waves
wave 1: foundation
wave 2: api, ui        (parallel)
wave 3: notifications

## Slices
### foundation
owns: prisma/schema.prisma, src/types/opt-in.ts
depends on: none
does: ...
done when: criteria 1; migration applies on a fresh database
test: command to run

### api
...

## Risks
what could force a re-plan
```

7. **Checkpoint**: show the waves and slices and ask the user to approve or adjust. This is one of the human checkpoints of the graph. Do not start building without it.

## Rules
- Every acceptance criterion from the spec appears in exactly one slice's `done when`.
- Each slice names the command that proves it is done.
- Respect `decision.md`: a council decision is a constraint, not a suggestion.
