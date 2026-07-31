# memory/

Point-in-time facts: who the user is, what a project assumes, where things live.
Lower ceremony than `skills/` and **not invokable** — memory is recalled, not triggered.

See the sorting rule in the root README for skills-vs-memory. Short version: if it reads
as *"when X happens, do Y"* it belongs in `skills/`; if it reads as *"Z is true about this
user/project"* it belongs here.

## Layout

One fact per file, filed by type — these paths are what `skills/lesson-capture` writes to:

- `user/` — who the user is: role, expertise, environment, working preferences.
- `feedback/` — guidance given on *how to work*, both corrections and confirmed approaches. Always record the why.
- `project/` — goals, constraints, and state not derivable from the code or git history. Convert relative dates to absolute ones.
- `reference/` — pointers outward: URLs, dashboards, tickets, account names.

## File format

Frontmatter plus a short body, matching Claude Code's own memory format so a file can be
dropped straight into `~/.claude/projects/<project>/memory/` unchanged:

```markdown
---
name: <short-kebab-case-slug>          # must match the filename
description: <one line — this is what recall matches against>
metadata:
  type: user | feedback | project | reference
---

<the fact, stated plainly>

**Why:** <what makes it true / what incident established it>       # feedback + project
**How to apply:** <what to do differently because of it>           # feedback + project
```

Link related entries with `[[slug]]`. A slug may name another memory entry **or a skill** —
`[[replit-project-bootstrap]]` from a memory entry means "the skill by that name." A link to
something that doesn't exist yet is fine; it marks something worth writing, not an error.

## Rules

- **One fact per file.** Two facts in one file means neither can be updated or deleted cleanly.
- **Update in place, don't accumulate duplicates.** Check for an existing file on the same
  subject before adding a new one.
- **Delete what turns out to be wrong.** A stale memory is worse than a missing one.
- **Don't record what the repo already says.** Code structure, past fixes, and git history
  are already written down; memory is for what isn't.
- **No credentials, ever.** Names of secrets are fine (`RESEND_API_KEY` is set in Replit
  Secrets); values are not. See `skills/secrets-hygiene`.
- **Date anything time-sensitive** with an absolute date, so a reader knows when it was true.

## Installing

Memory is per-project on each machine. Copy or symlink the entries relevant to a project
into `~/.claude/projects/<project-dir>/memory/`, and add a one-line pointer per file to
that directory's `MEMORY.md` index.
