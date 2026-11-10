---
name: verify
description: eric-flow verify module. Prove a change works where it actually runs (local app, preview deployment, CLI) against its acceptance criteria, with evidence for each, including one unhappy path per criterion. Use when the user asks to verify a change or PR with eric-flow, or runs /eric-flow:verify.
---

# verify

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Criteria**: from `spec.md`, else the PR description, else ask the user for the two or three behaviors that must work.
2. **Environment**: load context per `reference/context.md` and find how the project runs and previews changes, and how to sign in for testing (many projects document a helper). Prefer a preview or local environment. Never production.
3. **Plan the checks**: one check per criterion plus one unhappy path each. Independent checks on different surfaces (API vs UI) can run as parallel **verifier** subagents (`roles/verifier.md`); checks that share one browser session or one database state run in sequence.
4. **Run** them, capturing evidence (screenshots, responses, command output).
5. **Write `verify.md`**: per criterion, `pass | fail | not verified`, evidence, and the reason for anything not verified.
6. **Report**: failures first, then not verified, then passes.

## Rules
- Code that looks right is not verified. Only observed behavior counts.
- If the environment needs something only the human can provide (credentials, a sign-in, a device), ask once, specifically.
