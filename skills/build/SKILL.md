---
name: build
description: eric-flow build module. Implement an approved plan wave by wave, with one subagent per slice in its own git worktree, test-first, each slice reviewed against the spec before it is merged, then integrated and checked. Use when the user asks to build or implement an eric-flow plan, or runs /eric-flow:build.
---

# build

Paths: the plugin root is two directories above this skill's base directory.

## Preconditions
- `.flow/<slug>/plan.md` exists and the user approved it. If not, run `plan` first.
- The working tree is clean, or the user agreed to build on top of their changes.
- Follow the project's branch rules. Never build on the default branch: create `flow/<slug>` from it (or use the project's naming convention).

## Steps

1. **Read** `plan.md`, `spec.md`, `decision.md`, and context per `reference/context.md` (you need the check and test commands).
2. **For each wave, in order:**
   1. Create one worktree and branch per slice of the wave, from the integration branch, per `reference/parallel.md`.
   2. Launch one **slice builder** per slice (`roles/slice-builder.md` + the slice from `plan.md` + relevant spec criteria + decisions + explorer notes + the worktree path), all in one message.
   3. When they return, launch one **slice reviewer** per `done` slice (`roles/slice-reviewer.md`), all in one message.
   4. For a slice with verdict `fix`: send the findings back to a slice builder in the same worktree. Max two fix rounds per slice; after that, mark it blocked.
   5. Merge every passing slice branch into the integration branch. On a conflict, resolve it yourself (you have the whole picture) and note it.
   6. Run the project's check and tests on the integration branch. If they fail, fix before the next wave: later waves build on this one.
   7. Remove the wave's worktrees.
3. **Write `build.md`**: per slice, status, branch, tests run, notes; then the integration result.
4. **Report**: what was built, what is blocked and why, and the command output that proves the integration passes.

## Rules
- A blocked slice stops its dependents, not the whole build: independent slices continue.
- Never mark the build done without running the check and tests yourself on the integrated branch.
- Do not push or open a PR here unless the user asked; `feature` and the user decide that.
