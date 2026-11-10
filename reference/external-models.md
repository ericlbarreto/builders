# External models and fallback chains

builders routes some roles to models outside Claude. **Every one is optional.** Walk the chain for the role, use the first link that is installed **and** signed in, and tell the user which link ran.

## Detect once per session

```bash
for c in codex cursor-agent agent grok; do printf '%s: ' "$c"; command -v "$c" || echo missing; done
```

`agent` is the newer name of the Cursor CLI binary; treat it as `cursor-agent` when only it exists.

## Chain: second model (adversarial review, second opinions)

The goal is a **different model family** from Claude, usually GPT.

1. **Codex CLI** (`codex`), if installed and signed in:
   ```bash
   codex exec '<prompt>'
   ```
2. **Cursor CLI** (`cursor-agent` or `agent`), choosing a GPT model:
   ```bash
   cursor-agent --help            # once: confirm print mode and the model flag
   cursor-agent -p '<prompt>' --model <gpt-model> --output-format text
   ```
   Pick a GPT model from the models the installed version lists. If none is listed, use any non-Claude model.
3. **Claude subagent** with `roles/adversary.md`. Say in the output: "no second model family available, review ran on Claude only".

A link counts as **failed** and the chain moves on when: the binary is missing, it asks to log in, it errors, or it returns nothing in 5 minutes. Never stop the module because of a failed link. When a sign-in is the only problem, mention the fix once at the end (`codex login`, `cursor-agent login`).

Every second-model prompt says: read-only, do not edit files, return findings in the lens reviewer format, and where to find the diff (a path, or `git diff <base>...HEAD`).

## Chain: research (recent and live information)

1. **Grok** (`grok`):
   ```bash
   grok -p '<prompt>'
   ```
   Always `-p`. Every prompt names the source ("Search X and the web"), a time window ("since 2026-01-01"), the output shape ("5 items, each with URL and date"), and an honesty clause ("If you cannot find real sources, say NO_RESULTS. List what you could not confirm.").
2. **Cursor CLI** with a model that can browse, using the same prompt, when the installed version supports web access.
3. **The session's web search and fetch tools**, same prompt discipline.
4. **Nothing external available**: answer from the codebase and docs only, and mark every external fact `unverified`.

Report only what came back, with its URLs. Never add a source the tool did not return.
