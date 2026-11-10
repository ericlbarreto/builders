# Loading project context

builders knows nothing about a project until it reads it. Every module that makes judgments starts here.

## Read, in this order, whatever exists

1. Agent instructions: `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/`, `.claude/rules/`.
2. Domain docs: `CONTEXT.md`, `docs/`, `docs/adr/` (titles first, open the relevant ones).
3. Project lenses: `.builders/lenses/*.md`.
4. The task's artifacts: `.builders/<slug>/*.md`.
5. The linked ticket or PR, when the user gave one (use the tracker's tools or `gh`).
6. `README.md` and the manifest (`package.json`, `pyproject.toml`, `go.mod`...) to learn the stack and the commands for check, test and run.

Do not read everything in `docs/`. Skim titles and open what touches the task.

## Extract

Keep a short context note in your working memory (not a file) with:

- **Invariants**: rules the project states as must or never.
- **Commands**: how to check, test, run and preview.
- **Domains**: the areas the project cares about (billing, messaging, auth...).
- **Where things live**: the directories that matter for this task.

Modules pass the relevant part of this note into subagent briefs, since subagents do not read it themselves.

## Project rules win

When a project's own instructions conflict with builders defaults (PR format, branch names, test commands, review rules), follow the project.
