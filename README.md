# CLAUDE-SKILLS

Private repo for design principles and recursive learning — capturing lessons from past sessions as reusable Claude Code skills, so mistakes don't repeat across machines and projects.

## Structure

- `skills/` — behavioral, trigger-based procedures. Installed into `~/.claude/skills/<name>/SKILL.md` on any machine.
- `memory/` — point-in-time facts (user preferences, project state, references). Lower ceremony, not invokable.
- `queue/` — candidate lessons drafted after a session but not yet approved/promoted to `skills/` or `memory/`.

## Sorting rule: skills/ vs memory/

Ask: **"Would a fresh Claude, in an unrelated project, hitting this same situation, benefit from being handed a procedure right now?"**

- **Yes → `skills/`.** The lesson is behavioral and re-triggerable: it changes what to *do* at a recognizable decision point, independent of any one project's facts. A skill needs three things: a trigger condition, a concrete procedure/checklist, and independence from project-specific data.
- **No, it's a fact about a specific user/project/system → `memory/`.** The lesson is recall, not procedure: "user Z prefers W," "project X uses Y," "as of date D, system state was S."

Rule of thumb: if the lesson reads as "when X happens, do Y, in this order" → skill. If it reads as "Z is true about this user/project" → memory.

## Recursive learning loop

1. After a session with a real correction or a validated non-obvious approach, draft a candidate generalized rule.
2. Append it to `queue/` rather than auto-committing to `skills/` or `memory/`.
3. Review queued candidates in batches; promote, merge into an existing skill, or discard.
4. Promoted skills get symlinked or copied into `~/.claude/skills/` on each machine.
