# External models and fallback chains

builders routes some roles to models outside Claude. **Every one is optional.** Walk the chain for the role, use the first link that is installed **and** signed in, and tell the user which link ran.

## Detect once per session

```bash
export PATH="$HOME/.grok/bin:$HOME/.local/bin:$PATH"
for c in codex cursor-agent agent grok; do printf '%s: ' "$c"; command -v "$c" || echo missing; done
codex login status 2>&1 | head -1
```

The `PATH` line matters: the Grok installer puts the binary in `~/.grok/bin` and only adds it to interactive shells, so a tool shell may not see it. Use the same `PATH` on every call to these CLIs.

`agent` is the newer name of the Cursor CLI binary; treat it as `cursor-agent` when only it exists.

On a machine with no browser (a remote box), the sign-in fixes are `codex login --device-auth` and `grok login --device-auth`. Both print a link and a code to open on another device.

## Chain: second model (adversarial review, second opinions)

The goal is a **different model family** from Claude, usually GPT.

1. **Codex CLI** (`codex`), if installed and signed in:
   ```bash
   codex exec '<prompt>'
   ```
2. **Cursor CLI** (`cursor-agent` or `agent`), choosing a GPT model:
   ```bash
   cursor-agent status            # must say "Logged in"
   cursor-agent models            # once: list the model ids this account has
   cursor-agent -p '<prompt>' --model <gpt-model> --mode ask --trust --output-format text
   ```
   - `--mode ask` keeps the run read-only. `--trust` is required in print mode, or it stops to ask whether the directory is trusted. Run it from the repository root.
   - Pick a GPT model from `cursor-agent models`: prefer a `gpt-*-codex` id at default effort, then any other `gpt-*`. If no GPT id is listed, use any non-Claude model. Model ids change often, so never hardcode one.
3. **Claude subagent** with `roles/adversary.md`. Say in the output: "no second model family available, review ran on Claude only".

A link counts as **failed** and the chain moves on when: the binary is missing, it asks to log in, it errors, or it returns nothing in 5 minutes. Never stop the module because of a failed link. When a sign-in is the only problem, mention the fix once at the end (`codex login`, `cursor-agent login`).

Every second-model prompt says: read-only, do not edit files, return findings in the lens reviewer format, and where to find the diff (a path, or `git diff <base>...HEAD`).

## Chain: research (recent and live information)

1. **Grok** (`grok`):
   ```bash
   grok -p '<prompt>'
   ```
   Always `-p`. Every prompt names the source ("Search X and the web"), a time window ("since 2026-01-01"), the output shape ("5 items, each with URL and date"), and an honesty clause ("If you cannot find real sources, say NO_RESULTS. List what you could not confirm.").
2. **Cursor CLI with a Grok model** (a `grok-*` id from `cursor-agent models`), same prompt and the same flags as the second-model chain. Grok through Cursor may not search X or the live web: if the answer has no URLs, treat it as model knowledge, mark it `unverified`, and go on to link 3 for sources.
3. **The session's web search and fetch tools**, same prompt discipline.
4. **Nothing external available**: answer from the codebase and docs only, and mark every external fact `unverified`.

Report only what came back, with its URLs. Never add a source the tool did not return.
