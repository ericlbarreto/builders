# eric-flow

A project-agnostic development harness for Claude Code. Every stage of the work (spec, triage, research, council, plan, build, review, verify, learn) is a module that works on its own or chained by `/feature`, and independent work runs as parallel subagents.

Status: 0.2.0, first versions of every module. See [docs/design.md](docs/design.md).

## Modules

| Command | What it does |
| --- | --- |
| `/eric-flow:review <pr\|branch>` | parallel multi-lens + second-model review, findings verified and ranked; `--fix` applies them |
| `/eric-flow:triage` | size a change: domains, risk, lenses, research and decisions it needs |
| `/eric-flow:research` | answer questions with cited sources, in parallel |
| `/eric-flow:council` | settle a decision through a bounded, judged debate |
| `/eric-flow:spec` | idea or ticket to spec, with one batch of questions |
| `/eric-flow:plan` | slice a spec into file-disjoint parallel waves |
| `/eric-flow:build` | build a plan, one worktree per slice |
| `/eric-flow:verify` | prove a change works where it runs |
| `/eric-flow:learn` | turn caught mistakes into lens rules, through PRs |
| `/eric-flow:feature` | the whole graph, three human checkpoints |

## Optional external models

Each role walks a fallback chain and reports which link ran:

- second-model review: Codex, then Cursor CLI with a GPT model, then Claude
- research: Grok, then Cursor CLI, then web search

None are required.

## Install

```bash
claude plugin marketplace add ericlbarreto/eric-flow
claude plugin install eric-flow@eric-flow
```

Or inside Claude Code: `/plugin marketplace add ericlbarreto/eric-flow`, then `/plugin install eric-flow@eric-flow`.

## Update

```bash
claude plugin marketplace update eric-flow
```

## Use it in a project

Add to the project's `.claude/settings.json` so collaborators are offered the plugin:

```json
{
  "extraKnownMarketplaces": {
    "eric-flow": { "source": { "source": "github", "repo": "ericlbarreto/eric-flow" } }
  },
  "enabledPlugins": { "eric-flow@eric-flow": true }
}
```

Project-specific rules go in that project's `.flow/lenses/*.md`.
