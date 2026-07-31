---
name: verification-before-completion
description: Use before claiming any work is done, fixed, passing, or working. Gates the claim on having run the thing and read the output, not on having made the change.
version: 1.0.0
---

# Verification Before Completion

**Iron law: "DONE" IS A CLAIM ABOUT OBSERVED BEHAVIOR, NOT ABOUT WORK PERFORMED.**

## Usage

- `/verification-before-completion`
- Triggered before saying: "fixed," "done," "working," "tests pass," "deployed," "the bug is gone," or handing a task back to the user as complete.

## Checklist

1. **Run the thing.** An edit that compiles is not an edit that works. Execute the code path, hit the endpoint, load the page, run the test — the specific one that was failing.
2. **Read the actual output**, don't infer success from absence of a crash. A process that starts cleanly, a build that exits 0, and a job that finishes are all compatible with the feature still being broken.
3. **Verify against the original failing case**, not a similar one you constructed. If the bug report said "orders stuck in `pending` after fulfilment," reproduce that exact state and check it.
4. **Check the layer the user cares about.** A passing unit test does not confirm the UI renders; a 200 from the API does not confirm the right rows changed. Verify at the altitude the request was made at (see `replit-project-bootstrap` for the "started ≠ reachable" case).
5. **If you could not verify, say so explicitly** and state what would verify it. "Applied the fix; I could not run it because the dev server needs a restart I didn't want to trigger" is an honest and useful report. "Fixed" is not.
6. **State the evidence alongside the claim** — the command run, the status code, the row count, the test name. A claim with its evidence attached is checkable; one without is a request for trust.

## Failure scenarios this prevents

- A fix is written, reads correctly, and is reported as done — but was never executed, and fails on the first real input.
- A test suite is run, the summary line is skimmed, and a pre-existing failure is reported as "all passing."
- A change is verified against a freshly constructed happy-path case while the original reported reproduction still fails.
- Code changes are made to a service that bundles at build time, verified against the still-running old build, and reported as working.

## Guardrails

- Proportionality: a one-line comment change doesn't need an end-to-end run. This scales with the blast radius of being wrong.
- This gates *your own* claims. For a subagent's or teammate's claim reaching you, use `verify-delegated-work`. For the language used in any claim, verified or not, use `assumption-vs-fact-labeling`.
- Verification that is itself unverified doesn't count — confirm the test actually ran the new code (rebuilt, restarted, cache cleared) before trusting a green result.
