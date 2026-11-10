---
name: learn
description: eric-flow learn module. Turn what reviews and verification caught into durable lens rules, committed through a pull request - to the eric-flow repository when the rule is about process, to the project's .flow/lenses when it is about that codebase - never to one machine's memory. Use when the user asks eric-flow to learn from a task or past reviews, or runs /eric-flow:learn.
---

# learn

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Collect**: `review.md` and `verify.md` from the current task, or from every `.flow/*/` the user points at. Keep findings that were verified real (or failed verification checks).
2. **Ask why it got through**: for each, which stage should have caught it earlier? No lens covered it, a lens covered it vaguely, the spec missed a criterion, or the plan split it badly.
3. **Draft rules**: one short, checkable rule per root cause, written as a `Hunt for` or `Rules` bullet for a specific lens. Skip one-offs that will not recur.
4. **Place each rule**:
   - **project**: true only for this codebase (its invariants, its domains) → `.flow/lenses/<lens>.md` in the project. Create the lens file in the format from `lenses/README.md` if it does not exist.
   - **general**: true for any project → `lenses/<lens>.md` (or a role or skill file) in the eric-flow repository.
   - **personal**: a preference, not a defect → suggest a line for the user's `~/.claude/CLAUDE.md` instead.
5. **Show the user** every rule with its origin (task, finding, file) and its destination. Wait for their go-ahead. They may edit or drop rules.
6. **Open PRs**: one for the project, one for eric-flow, each on its own branch, following each repository's PR conventions. Each rule's origin goes in the PR body.
7. **Report** the PR links.

## Rules
- Never write to the agent's memory directory for this; rules must travel with git.
- Never commit to a default branch directly.
- A rule that would have flagged most correct changes too is noise: drop it.
