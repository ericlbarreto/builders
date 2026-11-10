# builders

A project-agnostic development harness for Claude Code. Every stage of the work (spec, triage, research, council, plan, build, review, verify, learn) is a module that works on its own or chained by `/feature`, and independent work runs as parallel subagents.

Status: 0.3.0, first versions of every module. See [docs/design.md](docs/design.md).

## Modules

| Command | What it does |
| --- | --- |
| `/builders:review <pr\|branch>` | parallel multi-lens + second-model review, findings verified and ranked; `--fix` applies them |
| `/builders:triage` | size a change: domains, risk, lenses, research and decisions it needs |
| `/builders:research` | answer questions with cited sources, in parallel |
| `/builders:council` | settle a decision through a bounded, judged debate |
| `/builders:spec` | idea or ticket to spec, with one batch of questions |
| `/builders:plan` | slice a spec into file-disjoint parallel waves |
| `/builders:build` | build a plan, one worktree per slice |
| `/builders:verify` | prove a change works where it runs |
| `/builders:learn` | turn caught mistakes into lens rules, through PRs |
| `/builders:feature` | the whole graph, three human checkpoints |

## Optional external models

Each role walks a fallback chain and reports which link ran:

- second-model review: Codex, then Cursor CLI with a GPT model, then Claude
- research: Grok, then Cursor CLI, then web search

None are required.

## Install

```bash
claude plugin marketplace add ericlbarreto/builders
claude plugin install builders@builders
```

Or inside Claude Code: `/plugin marketplace add ericlbarreto/builders`, then `/plugin install builders@builders`.

## Update

```bash
claude plugin marketplace update builders
```

## Use it in a project

Add to the project's `.claude/settings.json` so collaborators are offered the plugin:

```json
{
  "extraKnownMarketplaces": {
    "builders": { "source": { "source": "github", "repo": "ericlbarreto/builders" } }
  },
  "enabledPlugins": { "builders@builders": true }
}
```

Project-specific rules go in that project's `.builders/lenses/*.md`.
