# Candidate lessons

Drafted after a session, not yet promoted. Append new entries at the bottom using the
format in `skills/lesson-capture/SKILL.md`. Review in batches; each entry ends up
promoted to `skills/`, merged into an existing skill, moved to `memory/`, or discarded.

Delete an entry once it has been actioned — this file is a queue, not a log.

---

## apply-skills-retroactively — 2026-07-31

**Trigger type:** correction
**Source:** "let's run those skills on what we have worked on so far" — asked after a skill set was installed but only being treated as guidance for future work.
**Generalized rule:** Adopting a new skill/rule set is not the end of the task. Immediately re-audit the work already completed in the session against the newly available skills, before moving on. Newly installed discipline applies backwards to the same session, not only forwards.
**Why:** The install turn made claims ("all resolve", "no dangling references") that were only re-checked because the user prompted a retrospective pass — and that pass found two claims stated more strongly than their evidence. Low confidence that this needs its own skill rather than a clause in `verification-before-completion`; a single instance so far.
**Proposed home:** skills/verification-before-completion (merge) — as a "verify claims made earlier in the session, not just the current one" clause.

## bundled-approval-scope — 2026-07-31

**Trigger type:** confirmation
**Source:** An unqualified "go ahead" in response to an offer containing two distinct actions (install locally + open a PR on a remote repo); both were executed without re-asking, and the user did not push back.
**Generalized rule:** When the user answers a multi-part offer with a single unqualified go-ahead, execute the whole bundle as offered — re-asking item by item is its own failure. The scope of the approval is the scope of what was explicitly offered, no wider and no narrower.
**Why:** Counterbalance to `destructive-action-confirmation` step 4 ("a prior approval does not extend to this instance"), which correctly guards against scope creep but, read alone, encourages re-confirming things the user already approved in the same breath. The distinction: same-turn explicit offer → covered; similar action later → not covered.
**Proposed home:** skills/destructive-action-confirmation (merge) — as a clarifying clause on step 4.
