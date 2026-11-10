---
name: feature
description: builders full graph. Take a feature from idea or ticket to a verified pull request - spec, triage, parallel discovery (lenses, code map, research), council on open decisions, plan, parallel build, multi-model review, verification and learning - stopping only at three human checkpoints. Triage sizes the graph so small changes skip stages. Use when the user asks builders to build a feature end to end, or runs /builders:feature.
---

# feature

Paths: the plugin root is two directories above this skill's base directory. Each stage below is a module skill in this plugin; read that module's `SKILL.md` and follow it, rather than improvising the stage.

## The graph

```
0. context       reference/context.md
1. spec          skills/spec          [human: one batch of questions]
2. triage        skills/triage        sizes everything below
3. discover   || lenses on the spec + explorer + research
4. council       skills/council       only if triage listed decisions
5. plan          skills/plan          [human: approve the plan]
6. build      || skills/build
7. review     || skills/review --fix  on the integrated branch
8. verify        skills/verify
9. ship          push + PR, following the project's PR rules
10. learn        skills/learn         [human: approve rules]
```

## Sizing (from triage)

| Size | Run |
| --- | --- |
| small | spec only if unclear, build (single slice, no plan checkpoint unless risky), review, verify, ship |
| medium | spec, triage, discover (lenses only), plan, build, review, verify, ship, learn if review found something |
| large | everything |

Tell the user the size and the stages you will run, in one line, then go. They can override.

## Stage 3: discover (parallel)

In **one message**, launch together:
- one lens reviewer per triage lens, reviewing the **spec** (not code): what is missing, risky or wrong in it (`roles/lens-reviewer.md`)
- the explorer if triage did not already run one
- one researcher per research question (the `research` module's step 4)

Then fold lens findings into `spec.md` (add criteria, constraints, or open questions) and write `research.md`. A finding that needs a choice goes to council.

## Between stages

- Before each stage, check its artifact: if it exists and no input is newer, skip it (the run is resumable).
- After each stage, one line to the user: what came out, what is next. No long recaps.
- If a stage reveals the task is bigger or riskier than triage said, re-run triage and say so.

## Stage 9: ship

Push the integration branch and open a PR following the project's conventions (draft by default when the project says so). The PR body links the spec criteria and summarizes `review.md` and `verify.md`. Do not merge.

## Stop and ask when
- a human checkpoint is reached
- a slice stays blocked after its fix rounds
- review returns `rethink`
- verification fails on a criterion after one fix attempt
- something would write to production or a shared database
