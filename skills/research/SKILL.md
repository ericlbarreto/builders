---
name: research
description: builders research module. Answer one or more research questions with cited sources, in parallel, using Grok for recent or live information when installed, web search and official docs otherwise, and the codebase first. Keeps what sources say apart from inference. Use when the user asks to research something with builders, runs /builders:research, or when triage lists unknowns.
---

# research

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Collect questions**: from the user's message, or from the `Research` section of `.builders/<slug>/triage.md`. Rewrite each as one specific, answerable question. Merge near-duplicates.
2. **Load context** per `reference/context.md` (light: you need the domain and stack, not every invariant).
3. **Detect tools** per `reference/external-models.md` (Grok, web search).
4. **Fan out**: one **researcher** subagent (`roles/researcher.md`) per question, all in one message, following `reference/parallel.md`. Each brief includes which tools exist and how to call them.
5. **Join**: check that every source is a real URL or path from a researcher's output. Flag answers that disagree with each other.
6. **Write `research.md`**: one section per question, in the researcher's format, plus a top `## Bottom line` with one line per question.
7. **Report** the bottom line and the gaps.

## Rules
- Never add a source no researcher returned.
- A rule, price, limit or version stated without a source is marked `unverified`.
- If an answer changes the plan of the task (a regulation forbids the approach, an API does not exist), say so first.
