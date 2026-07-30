---
name: assumption-vs-fact-labeling
description: Use in any status update, handoff, or report — label what was confirmed by reading actual output versus what is inferred or assumed. Applies beyond debugging to all communication.
version: 1.0.0
---

# Assumption vs Fact Labeling

**Iron law: A CONFIRMED FACT AND AN INFERENCE ARE NEVER STATED IN THE SAME VOICE.**

## Usage

- `/assumption-vs-fact-labeling`
- Triggered in: status updates, session handoffs, bug reports, findings writeups, any summary of what was done or discovered — not only inside a debugging flow (`systematic-debugging` covers that narrower case).

## Checklist

1. **Before stating something as true, ask: did I read the actual output, or am I inferring/assuming/recalling?**
2. **Confirmed facts** get stated plainly: "the file contains X" (after Read), "the test passed" (after running it), "the endpoint returned 200" (after the request).
3. **Inferences and assumptions get explicit hedges**: "likely," "based on the pattern elsewhere," "I haven't verified this but," "assuming X behaves like Y."
4. **Never upgrade an inference to a fact by omission.** Dropping the hedge in a later message because it's inconvenient or the earlier caveat got lost is a common failure mode — restate the hedge each time the claim recurs.
5. **When relaying someone else's claim** (a subagent, a tool's self-report, documentation), attribute it: "the subagent reports X" rather than asserting X directly — see `verify-delegated-work`.
6. **In written reports/findings**, separate a "Confirmed" section from a "Suspected / not yet verified" section rather than blending them into one prose narrative.

## Failure scenarios this prevents

- A handoff says "the bug is fixed" when actually the fix was only applied and never run against the failing case.
- A vulnerability finding conflates "the parameter appears reflected" (observed) with "this is exploitable" (unverified) in the same sentence, misleading triage.
- A multi-turn conversation drifts from "I think X" to "X is the case" purely through repetition, without new evidence ever confirming it.

## Guardrails

- This is a communication discipline, not a call for hedging everything — once something is verified, state it plainly and move on. Over-hedging confirmed facts is its own failure mode.
- Pairs with `verification-before-completion`: that skill gates the *action* of claiming done; this skill governs the *language* used in every claim, done or not.
