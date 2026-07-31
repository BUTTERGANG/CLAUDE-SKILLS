---
name: systematic-debugging
description: Use when something is broken and the cause is not yet known. Enforces reproduce → isolate → hypothesise → test one variable at a time, instead of speculative shotgun fixes.
version: 1.0.0
---

# Systematic Debugging

**Iron law: DO NOT CHANGE CODE UNTIL YOU CAN STATE, IN ONE SENTENCE, WHAT IS ACTUALLY HAPPENING.**

## Usage

- `/systematic-debugging`
- Triggered when: a test fails for unclear reasons, behaviour differs between environments, an error message doesn't obviously map to a cause, or a first attempted fix didn't work.

## Process

1. **Reproduce it deliberately.** Find the smallest reliable command or input that triggers the failure. An intermittent bug you cannot summon on demand cannot be confirmed fixed either (see `verification-before-completion`).
2. **Read the actual error.** The whole trace, the whole log line, the real status code — not a paraphrase from memory and not the first line only. Most misdiagnoses start here.
3. **Establish what layer it fails at** before touching any code: is it the request, the handler, the query, the data, the build, or the environment? Bisect the pipeline with observations (log, curl, direct query), not with edits.
4. **Write the hypothesis down** as a falsifiable statement: "X fails because Y is null when Z happens." If you cannot phrase it that way, you are still in step 3.
5. **Change one variable at a time**, and re-check after each. Stacked speculative changes make the eventual fix unattributable — you no longer know which change mattered, or what the other ones broke.
6. **Confirm the causal link before declaring the cause.** The symptom disappearing after a change is correlation; verify the mechanism (revert it and watch the failure return, if the failure is cheap to re-trigger).
7. **Back out the debris.** Remove debug logging, temporary flags, loosened checks, and commented-out code before calling it done.

## Failure scenarios this prevents

- Four plausible fixes applied at once; the bug goes away; nobody knows which one worked, and two of them introduced regressions.
- A "connection timeout" is treated as a code bug when it was a cold-start on an autosuspended database (see `neondb-connection-safety`).
- A stale build is debugged for an hour because the running process never picked up the edit — the failing layer was never established.
- An error is diagnosed from its first line while the actual cause sits four frames down the trace.

## Guardrails

- Do not disable the check, catch the exception, or add a retry to make the symptom go away without understanding it — that converts a visible bug into a silent one (see `no-silent-failures`).
- Do not reach for `git reset`/`checkout` to "start clean" mid-investigation without the `destructive-action-confirmation` checklist; uncommitted diagnostic work is easy to destroy.
- Time-box before escalating: if several hypotheses have been tested and falsified, report what has been ruled out rather than continuing to guess — a well-documented dead end is useful, a long silent one is not.
- Not every problem needs this. An obvious typo with an unambiguous error is just a fix.
