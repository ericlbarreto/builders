---
name: spec
description: builders spec module. Turn a loose idea, a ticket or a conversation into a clear spec with acceptance criteria, asking the human all open questions in a single batch instead of one at a time. Use when the user asks to write a spec with builders, runs /builders:spec, or starts a feature that is not yet well defined.
---

# spec

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Gather**: the user's words, the ticket (tracker tools), linked docs. Load context per `reference/context.md`. Set up the task folder per `reference/work-folder.md`.
2. **Map**: launch one **explorer** (`roles/explorer.md`, fast model) so questions are informed by the code, not asked blind.
3. **Draft first, ask second.** Write the spec you believe is right, marking every assumption with `[assumed]`.
4. **Ask once.** Turn the assumptions that matter into one batch of questions: at most 7, each answerable in a few words, each with your default answer. Anything with a safe default stays an assumption and is not asked. Use the session's question tool if it has one; otherwise list them in one message.
5. **Finalize** with the answers. Remove `[assumed]` marks that were confirmed, keep the ones the user left to you.
6. **Write `spec.md`**:

```markdown
## Problem
who has it, and what happens today

## Outcome
what is true when this is done, from the user's side

## Scope
in: ...
out: ...

## Acceptance criteria
1. Given ..., when ..., then ...   (each one testable)

## Constraints
project rules, deadlines, compatibility

## Assumptions
what was decided without asking

## Open
what is still unknown (these feed triage)
```

7. **Report** the outcome and the criteria in a few lines.

## Rules
- One round of questions. A second round only if an answer contradicts the rest of the spec.
- Acceptance criteria are observable behavior, never implementation steps.
