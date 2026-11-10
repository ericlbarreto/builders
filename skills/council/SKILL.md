---
name: council
description: eric-flow council module. Settle a design decision through a bounded debate - a proposer, two to four critics each speaking for a different lens, at most two rounds, and a judge who records the decision and the dissent. Questions that depend on business priority go to the human as one batch. Use when the user asks agents to debate or decide something with eric-flow, runs /eric-flow:council, or when triage lists open decisions.
---

# council

Paths: the plugin root is two directories above this skill's base directory.

## Steps

1. **Collect decisions**: from the user's message or the `Decisions for council` section of `triage.md`. Each must be a real choice with trade-offs. If one has an obvious answer from the code or the project's rules, answer it directly and drop it.
2. **Gather evidence**: load context (`reference/context.md`) and read `spec.md`, `research.md` and the explorer output if present. A council without evidence just trades opinions; if a decision hinges on an unknown fact, run `research` on it first.
3. **Propose**: for each decision, you write the proposal yourself: the option you would pick, why, and its known costs. Two or three sentences.
4. **Pick critics**: two to four lenses with the most to say about this decision (from triage, `lenses/`, `.flow/lenses/`). Distinct lenses only.
5. **Round 1**: one **critic** subagent per lens (`roles/critic.md` + its lens + the proposal + the evidence), all in one message. Run all decisions' critics in that same message.
6. **Reply**: for each decision, answer the critics: accept, adjust the proposal, or rebut with evidence.
7. **Round 2**, only where an objection was rebutted rather than accepted: the same critics, one message, with the reply and the other critics' turns.
8. **Judge**: one **judge** subagent per decision (`roles/judge.md`, strongest model), all in one message.
9. **Write `decision.md`** with every judgment.
10. **Ask the human** once: gather every `for the human` item from all decisions into a single batch of short questions. Record the answers in `decision.md`.

## Rules
- Max two rounds. No round 3, ever: an unresolved disagreement is what the judge and the dissent line are for.
- The judge decides on evidence. Taste and priorities go to the human.
