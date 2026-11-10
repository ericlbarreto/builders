---
name: flow
description: Index of the eric-flow harness. Use when the user asks what eric-flow can do, which flow module fits a task, or how the modules chain together.
---

# eric-flow

eric-flow is a project-agnostic development harness. Every stage is a module that works on its own or chained by `feature`. Modules hand off through files in `.flow/<task>/` in the project (see `reference/work-folder.md` at the plugin root, two directories above this skill).

| Module | Use it alone to | Writes |
| --- | --- | --- |
| `spec` | turn a loose idea or ticket into a spec, asking once | `spec.md` |
| `triage` | learn what a change touches, its risk, and which lenses, research and decisions it needs | `triage.md` |
| `research` | answer questions with cited sources, in parallel | `research.md` |
| `council` | settle a decision through a bounded, judged debate | `decision.md` |
| `plan` | slice work into file-disjoint parallel waves | `plan.md` |
| `build` | implement a plan, one worktree per slice | `build.md` |
| `review` | review any PR, branch or diff through lenses and a second model | `review.md` |
| `verify` | prove the change works where it runs | `verify.md` |
| `learn` | turn caught mistakes into lens rules, through PRs | PRs |
| `feature` | run the whole graph with three human checkpoints | all of the above |

Invoke a module as `/eric-flow:<module>`. Recommend the smallest module that does the job: a PR that only needs checking wants `review`, not `feature`.
