---
name: lesson-capture
description: Use immediately after the user corrects an approach or explicitly confirms a non-obvious one worked. Drafts a generalized candidate rule and queues it for review instead of letting the lesson evaporate at session end.
version: 1.0.0
---

# Lesson Capture

**Iron law: A CORRECTION OR A VALIDATED JUDGMENT CALL THAT ISN'T WRITTEN DOWN DIDN'T HAPPEN.**

## Usage

- `/lesson-capture`
- Triggered by two distinct signals — both matter, only one is easy to notice:
  1. **Correction** — user says "no not that," "don't," "stop doing X," or otherwise redirects an approach you took.
  2. **Confirmation** — user says "yes exactly," "perfect, keep doing that," or silently accepts an unusual/non-obvious choice without pushback (e.g. you picked one bundled PR over several small ones and they just moved on).

Confirmations are the harder trigger to catch — nothing forces attention to them the way a correction does. Watch for them deliberately.

## Process

1. **Isolate the generalizable core.** Strip project-specific names/paths/values. Ask: does this rule apply only here, or would it apply the next time I hit a structurally similar situation in an unrelated project?
   - Generalizes → candidate for `skills/` (behavioral) or `memory/feedback` (preference/style).
   - Doesn't generalize (true only about this user/project/system state) → candidate for `memory/user` or `memory/project` directly, no queue needed — see the sorting rule in this repo's README.
2. **Check for an existing home first.** Grep `~/.claude/skills/*/SKILL.md` and this repo's `memory/` for a skill or memory file this lesson could extend rather than duplicate. If found, note it as "merge into `<name>`" instead of drafting fresh.
3. **Draft the candidate** using the queue entry format below. Do this in the same turn the trigger fires, not deferred to end-of-session — deferred capture is where lessons get lost.
4. **Append to `queue/candidates.md`** in this repo (create if absent). Do not write directly into `skills/` or `memory/` — promotion is a separate, deliberate review step per the README's recursive-learning loop.
5. **Do not interrupt the current task to promote it.** Capture is a side effect, not a detour — draft, append, continue.

## Queue entry format

```markdown
## <short-slug> — <YYYY-MM-DD>

**Trigger type:** correction | confirmation
**Source:** <one-line quote or paraphrase of what the user said>
**Generalized rule:** <the rule stripped of project-specific detail>
**Why:** <what incident/reasoning prompted it>
**Proposed home:** skills/<name> (new) | skills/<name> (merge) | memory/<type>
```

## Failure scenarios this prevents

- A user corrects the same mistake three sessions in a row because the first correction was never written anywhere.
- A validated non-obvious judgment call (e.g. "single PR was right, don't split it") quietly reverts to default behavior next session because only corrections get remembered, not confirmations.
- Lessons pile up as vague end-of-session recollection instead of dated, sourced, reviewable entries.

## Guardrails

- One correction is a data point, not automatically a rule — if the generalized version feels like a stretch from a single instance, still queue it, but mark low confidence in the "Why" line rather than skipping capture.
- Do not auto-promote to `skills/` or `memory/` from this skill — that requires the separate review step so low-quality or overly narrow candidates don't pollute the active set.
- Don't queue lessons that are pure one-off facts already covered by ordinary memory-saving triggers (see this repo's README sorting rule) — only queue when there's genuine ambiguity or generalization work to do.
