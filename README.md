# CLAUDE-SKILLS

Private repo for design principles and recursive learning — capturing lessons from past sessions as reusable Claude Code skills, so mistakes don't repeat across machines and projects.

## Structure

- `skills/` — behavioral, trigger-based procedures. Installed into `~/.claude/skills/<name>/SKILL.md` on any machine.
- `memory/` — point-in-time facts (user preferences, project state, references). Lower ceremony, not invokable. Filed by type in `user/`, `feedback/`, `project/`, `reference/`; see [`memory/README.md`](memory/README.md) for the file format.
- `queue/` — candidate lessons drafted after a session but not yet approved/promoted to `skills/` or `memory/`. Entries land in [`queue/candidates.md`](queue/candidates.md).

## Installing on a machine

Clone this repo once, then symlink each skill into `~/.claude/skills/` so `git pull` updates them everywhere:

```sh
REPO=~/.claude/CLAUDE-SKILLS

# Clone if absent, pull if present. Never delete-and-reclone: a clone on this
# machine may hold skill edits that were never committed (see
# skills/destructive-action-confirmation).
if [ -d "$REPO/.git" ]; then
  git -C "$REPO" pull --ff-only
else
  git clone git@github.com:BUTTERGANG/CLAUDE-SKILLS.git "$REPO"
fi

mkdir -p ~/.claude/skills
for d in "$REPO"/skills/*/; do
  name=$(basename "$d")
  # install-skip.txt lists skills kept in the repo but not loaded
  if grep -qxF "$name" "$REPO/install-skip.txt" 2>/dev/null; then
    rm -f ~/.claude/skills/"$name"   # removes the symlink, not the repo copy
    continue
  fi
  ln -sfn "$d" ~/.claude/skills/"$name"
done
```

An idle skill costs only its name and description — roughly 50 tokens; the body
loads only when it fires. So context is a weak reason to drop one. The reasons
that hold up are that a skill is **wrong** (a trusted-and-wrong skill is worse
than none) or that it **duplicates default behavior**. Anything in
[`install-skip.txt`](install-skip.txt) failed the second test; the file records
why, and what still references it.

Because each installed skill is a symlink into the clone, **editing a skill in
`~/.claude/skills/` edits the repo working tree** — commit it there rather than
expecting the change to be local to one machine.

Memory is per-project rather than global — copy or symlink the relevant entries into `~/.claude/projects/<project-dir>/memory/` and index them in that directory's `MEMORY.md`.

## Sorting rule: skills/ vs memory/

Ask: **"Would a fresh Claude, in an unrelated project, hitting this same situation, benefit from being handed a procedure right now?"**

- **Yes → `skills/`.** The lesson is behavioral and re-triggerable: it changes what to *do* at a recognizable decision point, independent of any one project's facts. A skill needs three things: a trigger condition, a concrete procedure/checklist, and independence from project-specific data.
- **No, it's a fact about a specific user/project/system → `memory/`.** The lesson is recall, not procedure: "user Z prefers W," "project X uses Y," "as of date D, system state was S."

Rule of thumb: if the lesson reads as "when X happens, do Y, in this order" → skill. If it reads as "Z is true about this user/project" → memory.

## Recursive learning loop

1. After a session with a real correction or a validated non-obvious approach, draft a candidate generalized rule.
2. Append it to `queue/` rather than auto-committing to `skills/` or `memory/`.
3. Review queued candidates in batches; promote, merge into an existing skill, or discard.
4. Promoted skills reach each machine via the symlink install above — `git pull` in the clone is the whole update step.
