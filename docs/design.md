# eric-flow design

Status: 0.2.0. Every module exists as a first version and has not yet been run on real work. Expect the first runs to change them.

## Goals

1. **Less prompting.** Context goes in once, at the start. Agents read the project, decide what needs research or debate, and only stop at a few human checkpoints.
2. **Parallel by default.** Work that does not depend on other work runs at the same time. Only spec, plan and final verification are inherently sequential.
3. **Modular.** Every stage is a module usable on its own (`/review 1234` with no spec or plan) or chained by `/feature`.
4. **Project-agnostic.** The harness knows *how* to work. *What* matters in a given codebase comes from that project's own context.
5. **Self-improving.** Mistakes caught in review become rules, committed to git through a PR, never to one machine's memory.

## Non-goals

- Fully autonomous loops before the graph is reliable with a human watching.
- Forking existing skill sets. Ideas are borrowed, code is not vendored.
- Replacing project-specific tooling (CI, preview environments, login helpers). Modules call into it when a project provides it.

## Core concepts

### Graph

A graph is a map of steps (nodes) and dependencies (edges). A node runs as soon as everything it depends on is done, so independent nodes run in parallel. Graphs also branch (skip research for a small change) and loop (review, fix, review again, with a cap).

Parallel stages fan out by sending every subagent call of the stage in a single message, so they run at the same time (`reference/parallel.md`). When the user has opted into Claude Code's Workflow tool, a stage may run as a workflow script with the same briefs and join rules.

### Small by default

Short briefs, capped outputs (at most 5 findings and 300 words per subagent) and the fewest tests that prove a behavior. Small inputs and outputs give sharper results and keep the orchestrator from losing track when many subagents report at once.

### Lens

A lens is a single point of view: a subagent that looks at the same change hunting one class of problem. Three sources, merged at triage time:

1. **Built-in lenses** shipped in `lenses/`, valid for any project: security, data integrity, performance, tests, product, compliance/legal, maintainability.
2. **Derived lenses.** Triage reads the project context and the change, and writes ad hoc lenses for domains it discovers (for example "consent before messaging" when a change sends SMS).
3. **Project lenses** (optional) in the target repo at `.flow/lenses/*.md`, for fixed rules of that codebase.

### Work folder

Modules hand off through files, one folder per task in the target repo:

```
.flow/<task-slug>/
  spec.md  triage.md  research.md  decision.md  plan.md  review.md  verify.md
```

Each module reads whatever exists and ignores what is missing. That is what makes modules standalone, chainable and resumable across sessions. `.flow/<task-slug>/` is meant to be gitignored; `.flow/lenses/` is meant to be committed.

## Modules

| Module | Input | Output | Human checkpoint |
| --- | --- | --- | --- |
| `spec` | idea, ticket URL or text | `spec.md` | answers one batch of questions |
| `triage` | spec, ticket or diff | `triage.md`: domains touched, unknowns, risk, lenses to run, graph size | none |
| `research` | question or `triage.md` unknowns | `research.md` with sources, quotes kept apart from inference | none |
| `council` | question or findings in conflict | `decision.md` | only questions agents cannot settle |
| `plan` | spec (+ decision) | `plan.md`: slices with file ownership and dependencies | approves the plan |
| `build` | plan | one branch, slices built in parallel worktrees | none |
| `review` | PR number, branch or diff | `review.md`, findings ranked by severity | none, `--fix` applies fixes |
| `verify` | PR or branch | `verify.md` with evidence | none |
| `learn` | past `review.md` files | a PR adding rules to lenses | approves the PR |
| `feature` | idea or ticket | all of the above | the checkpoints above |

## The full graph (`/feature`)

```
0. context      read README, AGENTS.md/CLAUDE.md, docs, ADRs, linked ticket
1. spec         one batch of questions                          [human]
2. triage       decide lenses, research and graph size
3. || discover  lenses + codebase exploration + research
4. council      bounded debate on conflicts, a judge decides
5. plan         file-disjoint slices with dependencies          [human]
6. || build     one subagent per slice, own worktree, test-first,
                per-slice review against spec and quality
7. integrate    merge slices, run the project's checks and tests
8. || review    Claude + second model + lenses on the diff, fix loop (max 2)
9. verify       run where the project runs, collect evidence
10. learn       propose new lens rules                          [human]
```

Triage picks a size so small changes do not pay for the full graph:

| Size | Runs |
| --- | --- |
| small | spec (if unclear), build, review, verify |
| medium | adds triage lenses and plan |
| large | everything, including research and council |

## Council rules

- Triggered only when findings conflict or triage marks a decision as open.
- One proposer, two to four critics with distinct lenses, one judge.
- At most two rounds. The judge writes the decision and the dissent.
- Anything that depends on business priority or taste goes to the human as one batch of questions.

## Model routing

| Role | Default |
| --- | --- |
| orchestration, triage, judge, implementation | Claude (strongest available) |
| exploration, simple lenses | a smaller, faster Claude model |
| recent or web research | Grok, then Cursor with web access, then web search |
| adversarial review | Codex, then Cursor with a GPT model, then a Claude subagent |

Every external model is optional. Each role walks its fallback chain (`reference/external-models.md`), uses the first link that is installed and signed in, and reports which one ran. A missing or signed-out CLI never stops a module.

## Learning

`learn` never writes to machine memory. It opens a PR with the rule and its origin (which review caught it, on which change), against:

- the **eric-flow** repo when the rule is about process and holds for any project;
- the **target project** (`.flow/lenses/`) when the rule is about that codebase.

Personal preferences stay in `~/.claude/CLAUDE.md`.

## Borrowed ideas

| From | Idea |
| --- | --- |
| superpowers | implement and review loops, test-first, verify before claiming done, root-cause debugging |
| Matt Pocock's skills | interrogate an idea into a spec, slice it into tasks |
| leo-skills | second-model review, agent debate, Grok research |
| new here | triage that sizes the graph, lenses from project context, judged council, learning through PRs |

## Repository layout

```
.claude-plugin/marketplace.json   the marketplace and the plugin's skill list
skills/<module>/SKILL.md          one skill per module, plus the flow index
roles/                            subagent briefs (lens reviewer, adversary, triage, judge...)
lenses/                           built-in lenses
reference/                        shared contracts: work folder, parallel stages, context, external models
docs/design.md                    this file
```

Skills reference `roles/`, `lenses/` and `reference/` relative to the plugin root, two directories above each skill.

## Hardening order

All modules exist. They get proven on real work in this order:

1. `review` on real PRs.
2. `triage` and `research`.
3. `council`.
4. `spec`, `plan`, `build`, `feature`.
5. `learn`, once there are reviews to learn from.

## Open questions

- Should `.flow/<task-slug>/` live in the target repo (gitignored) or under `~/.flow/` keyed by repo?
- How strict should file ownership be between parallel slices when a shared file (a schema, a router) is unavoidable?
- Token budget per graph size, and whether triage should print an estimate before running a large graph.
