---
name: verify-delegated-work
description: Use when a subagent, background task, or teammate reports a result before you relay or act on it. A delegated summary describes intent, not necessarily what happened — check before trusting.
version: 1.0.0
---

# Verify Delegated Work

**Iron law: A DELEGATE'S SELF-REPORT IS A CLAIM, NOT EVIDENCE.**

## Usage

- `/verify-delegated-work`
- Triggered whenever you receive output from: a subagent (Agent tool), a background task, a workflow stage, or a teammate — and are about to relay its summary to the user or build on it as fact.

## Why this is distinct from verification-before-completion

`verification-before-completion` gates *your own* claims of done/fixed/passing. This skill gates *someone else's* claim reaching you — the failure mode is different: the delegate may be confident and articulate while having done something other than what it says, especially for code edits, searches, or multi-step tasks.

## Checklist

1. **Read the actual artifact, not just the summary.** If a subagent says "fixed the bug," check the diff. If it says "found 3 matches," check the actual matches. If it says "tests pass," check the test output.
2. **Check for scope drift.** Did the delegate do what was asked, or something adjacent/broader/narrower that it's describing as equivalent?
3. **Treat empty/null results with suspicion.** A subagent returning nothing, or a workflow stage returning `null`, is not the same as "confirmed no results" — check whether it failed silently (see `no-silent-failures`).
4. **Don't relay a summary as your own finding.** If you haven't independently spot-checked it, say "the subagent reports X" rather than asserting X as verified fact.
5. **For parallel/background work**, don't fabricate or predict results before they land — wait for the actual notification, then verify it same as above.

## Failure scenarios this prevents

- A subagent reports "reviewed all 40 files, no issues found" when it actually hit a read limit and only saw 10.
- A background job's completion notification is treated as proof of correctness rather than proof of exit-without-crash.
- A teammate agent's "done" message is relayed to the user without checking that the described file changes actually exist.

## Guardrails

- This is not about distrust of the delegate's competence — it's about the structural fact that a summary is generated to describe intent, and can diverge from ground truth without any bad faith involved.
- Spot-check proportional to stakes: a quick grep for an internal search task; a full diff read for anything touching production code or user-facing claims.
