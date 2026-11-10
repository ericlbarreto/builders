---
name: flow
description: Index of the eric-flow harness. Use when the user asks what eric-flow can do, which flow module fits a task, or how the modules chain together.
---

# eric-flow

eric-flow is a project-agnostic development harness. Every stage is a module that works on its own or chained by `/feature`.

| Module | Status | Use it alone to |
| --- | --- | --- |
| `spec` | planned | turn a loose idea or ticket into a spec |
| `triage` | planned | learn what a change touches and how risky it is |
| `research` | planned | research a topic with cited sources |
| `council` | planned | settle a decision through a bounded debate between lenses |
| `plan` | planned | slice work into parallel, file-disjoint pieces |
| `build` | planned | implement an existing plan |
| `review` | planned | review any PR, branch or diff |
| `verify` | planned | prove a change works where it runs |
| `learn` | planned | turn review findings into lens rules, via a PR |
| `feature` | planned | run the whole graph |

Tell the user which modules exist today (the Status column) and point them at `docs/design.md` in the eric-flow repository for the full design.
