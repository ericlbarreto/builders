# eric-flow

A project-agnostic development harness for Claude Code. Every stage of the work (spec, triage, research, council, plan, build, review, verify, learn) is a module that works on its own or chained by `/feature`, and independent work runs as parallel subagents.

Status: early. See [docs/design.md](docs/design.md) for the design and the build order.

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
