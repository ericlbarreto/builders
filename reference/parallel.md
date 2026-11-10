# Running a parallel stage

A parallel stage fans out to subagents that do not depend on each other, then joins their results.

## Keep everything small

Small inputs and small outputs give better results, and keep the orchestrator from losing track when many subagents report at once.

- **Briefs:** only what the subagent needs. Paste the role and the lens, point at files by path instead of pasting them, and pass at most 10 lines of project context.
- **Outputs:** every brief ends with a hard limit: at most **5 findings**, at most **300 words**, in the role's exact format. Ask for the most severe first so the cut drops the least important.
- **Artifacts:** each `.flow/` file leads with a short summary. Detail goes below it and stays proportional to the task.
- **Tests:** the fewest that prove the behavior. See `roles/slice-builder.md`.

## Fan out

- Send **all subagent calls of the stage in one message**, so they run at the same time. Calls sent one per message run in sequence and waste the whole point.
- Each subagent gets a self-contained brief: it sees nothing of this conversation. Include the role file content (from `roles/`), the lens content (from `lenses/`) when there is one, the artifact paths to read, the exact output format, and the scope limits.
- Pick the model per role (see the table below). Pass it as the subagent's model.
- Cap a single stage at **8 subagents**. If more lenses apply, merge the closest ones into one brief.

| Role | Model |
| --- | --- |
| explorer, simple lens, finding verifier | haiku or sonnet |
| lens reviewer, researcher, critic, slice builder, slice reviewer | sonnet |
| triage, judge, integration, anything that decides | the session's own model |

## Join

- Wait for every subagent. Never summarize a pending one.
- Merge results, drop duplicates (same file, same line, same cause), and keep the source of each item (which lens or model found it).
- Treat each result as a claim, not a fact. Anything that would change the outcome gets checked (read the code, run the command) before it is acted on.

## Isolation for writers

Read-only subagents (lenses, research, review) share the checkout. Subagents that **write code** each get their own git worktree, so two writers never touch the same working tree:

```bash
git worktree add ../<repo>-<slug>-<slice> -b flow/<slug>/<slice>
```

Remove worktrees after their branch is merged back (`git worktree remove <path>`).

## When the Workflow tool is available

If the user has opted into workflows (Claude Code's Workflow tool), a stage may run as a workflow script instead of hand-sent subagent calls. The contract is the same: same briefs, same models, same join rules.
