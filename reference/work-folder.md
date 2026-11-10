# Work folder contract

Every module reads and writes files in one folder per task, in the target repository:

```
.flow/
  lenses/              committed: project-specific lenses (optional)
  <task-slug>/         gitignored: one task's artifacts
    spec.md
    triage.md
    research.md
    decision.md
    plan.md
    build.md
    review.md
    verify.md
```

## Finding or creating the task folder

1. If the user names a slug, or the conversation already used one, use it.
2. Otherwise derive a slug from the task: lowercase, ascii, hyphens, at most 5 words (`sms-opt-in-flow`). For a PR review with no task, use `pr-<number>` or `branch-<branch-name>`.
3. If `.flow/<slug>/` exists, read every artifact in it before doing anything: a module may be resuming work.
4. On first write, make sure `.flow/*/` is ignored while `.flow/lenses/` is not. If the repository has a `.gitignore` without these lines, append:
   ```
   .flow/*
   !.flow/lenses/
   ```
   Tell the user you did it.

## Artifact format

Every artifact starts with a small header, then free markdown:

```markdown
---
module: review
task: sms-opt-in-flow
updated: 2026-11-09
inputs: [spec.md, plan.md]
---
```

`inputs` lists the artifacts this one was built from, so a later module knows whether it is stale (an input newer than the artifact means re-run).

## Rules

- A module reads every artifact that exists and **ignores what is missing**. Missing inputs make it do more discovery, never fail.
- A module only writes its own artifact. `review` never edits `plan.md`.
- Re-running a module overwrites its artifact. Keep the previous one as `<name>.prev.md` when the user may want to compare.
- Artifacts are for agents and humans alike: lead with the conclusion, then the detail.
