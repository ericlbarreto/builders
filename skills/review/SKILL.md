---
name: review
description: eric-flow review module. Review a pull request, branch or diff in parallel through several lenses plus a second model, verify every finding, and return them ranked by severity (optionally fixing them). Use when the user asks for an eric-flow review, a multi-lens or second-model review, or runs /eric-flow:review with a PR number, branch or path. Works standalone, no spec or plan needed.
---

# review

Paths: the plugin root is two directories above this skill's base directory. `roles/`, `lenses/` and `reference/` below are relative to it.

## Arguments

- target: a PR number or URL, a branch, a path, or nothing (current changes against the default branch)
- `--fix`: apply verified fixes after the review
- `--quick`: skip the second model and finding verification (small diffs only)

## Steps

### 1. Get the change
- PR: `gh pr view <n> --json title,body,baseRefName,headRefName,files` and `gh pr diff <n>`.
- Branch: `git diff $(git merge-base origin/<default> <branch>)...<branch>`.
- Nothing given: staged + unstaged changes, or the current branch against its merge base.
If the diff is empty, say so and stop.

### 2. Load context
Follow `reference/context.md`. Pick the task folder per `reference/work-folder.md` (`pr-<n>` or `branch-<name>` when there is no task). If `spec.md`, `plan.md` or `decision.md` exist, the review also checks the change against them.

### 3. Choose lenses
If `triage.md` exists and is newer than the change, use its lens list. Otherwise do a quick inline triage (read `roles/triage.md`, but decide only domains and lenses, not research or council):
- always `correctness`; `tests` unless the change is docs-only
- every built-in lens in `lenses/` whose `applies-when` matches
- every project lens in `.flow/lenses/` whose `applies-when` matches
- a derived lens for any touched domain no lens covers

### 4. Fan out (one message, all at once)
Follow `reference/parallel.md`. Launch together:
- one **lens reviewer** per lens (`roles/lens-reviewer.md` + the lens text + the diff location + relevant context)
- one **adversary** (`roles/adversary.md`) on a second model family, walking the chain in `reference/external-models.md`: Codex, then Cursor with a GPT model, then a Claude subagent. Run the CLI from a subagent or in the background so it does not block the other reviewers. Record which link ran.
- if a spec or plan exists, one **slice reviewer** pass on spec compliance only (`roles/slice-reviewer.md`, pass 1)

### 5. Join
Merge, dedupe (same location and same cause), keep which lens or model found each.

### 6. Verify (skip with `--quick`)
Every critical, high or medium finding goes to a **finding verifier** (`roles/finding-verifier.md`), all in one message. Drop `false`. Keep `uncertain` but label it. Low findings are kept unverified and labeled.

### 7. Write `review.md`
```markdown
## Summary
verdict: ship | fix first | rethink
<two sentences>

## Findings
### 1. [high] <what> (security, verified)
where: path:line
scenario: ...
fix: suggested change

## Checked and clean
<lens: what it checked>

## Not covered
<anything skipped: no second model, a lens that failed, files too large>
```

### 8. Report
Show the summary and the findings to the user, most severe first. Name what was not covered.

### 9. Fix (only with `--fix`, or when the user then asks)
Apply fixes for verified findings, smallest change that removes the cause. Run the project's check and tests. Then re-run steps 4 to 6 on the new diff once (max two review rounds in total). Report what was fixed and what remains.

## Rules
- Never post comments on a PR unless the user asks; when they do, one comment per finding at its line.
- Never mark a finding real because a reviewer said so: the verifier decides.
- The project's own review rules (AGENTS.md, a review skill) count as a project lens.
