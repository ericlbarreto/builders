---
name: triage
description: builders triage module. Size a task, ticket, spec or diff before work starts. Decides which domains it touches, which lenses to run (deriving new ones for uncovered domains), what needs research, what needs a council decision, the risk, and the size of the graph. Use when the user asks to triage or size a change with builders, or runs /builders:triage.
---

# triage

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Get the task**: a sentence, a ticket (use the tracker's tools), a spec (`.builders/<slug>/spec.md`), a PR or a diff.
2. **Load context** per `reference/context.md`. Set up the task folder per `reference/work-folder.md`.
3. **Map the code** when the task is not a diff yet: launch one **explorer** subagent (`roles/explorer.md`, a fast model). For a diff, the diff is the map.
4. **List available lenses**: every file in `lenses/` (skip `README.md`) and `.builders/lenses/`. Read their `applies-when` lines.
5. **Decide** following `roles/triage.md`. You do this yourself, not a subagent: it needs the whole picture.
6. **Write `triage.md`** in the format from `roles/triage.md`, including the full text of derived lenses.
7. **Report** in a few lines: size, risk, lenses, research questions, council questions. Ask nothing unless the task itself is too unclear to size; then suggest running `spec` first.

## Rules
- A derived lens must be grounded: cite the project file or the task text that made you create it.
- Prefer fewer, sharper lenses. Each costs a subagent in every later stage.
