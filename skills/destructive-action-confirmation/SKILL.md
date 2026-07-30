---
name: destructive-action-confirmation
description: Use before any hard-to-reverse or shared-state-affecting action — force-push, reset --hard, drop table, rm -rf, sending a message, closing a PR/issue, modifying shared infra. Confirm scope with the user before acting, not after.
version: 1.0.0
---

# Destructive Action Confirmation

**Iron law: IRREVERSIBLE OR SHARED-STATE ACTIONS GET CONFIRMED BEFORE, NOT EXPLAINED AFTER.**

## Usage

- `/destructive-action-confirmation`
- Triggered before: force-push, `git reset --hard`, `git clean -f`, `rm -rf`, dropping/truncating a table, killing a shared process, removing/downgrading a dependency, amending a published commit, sending an external message (Slack/email/GitHub comment), closing/merging a PR, modifying CI/CD config, changing shared infra or permissions.

## Checklist

1. **Classify the action** — is it (a) local + reversible, (b) hard to reverse, or (c) visible to others / shared state? Only (a) proceeds without confirmation by default.
2. **State the action plainly before running it** — what will run, what it affects, what cannot be undone if it goes wrong.
3. **Check for in-progress work you might be discarding.** Before checkout/restore/reset/clean on a repo, run `git status` — stash or commit anything found first rather than assuming it's disposable.
4. **A prior approval does not extend to this instance.** The user approving one `git push` does not mean all future pushes are pre-approved — confirm scope matches what was actually authorized, not what's merely similar.
5. **When durable instructions (CLAUDE.md, project rules) pre-authorize a class of action, that stands** — this skill governs the default, not cases where the user has already opted the agent into autonomy for this category.
6. **Prefer a reversible detour over a destructive shortcut.** Rename/move-aside/stash instead of delete when unsure whether something is needed.

## Failure scenarios this prevents

- A `git push --force` intended to fix one branch overwrites unrelated upstream commits from a teammate.
- `rm -rf` run to clean a build directory turns out to include an uncommitted work-in-progress file.
- An agent treats "you can push" from three tasks ago as blanket approval and pushes something the user hasn't seen.
- A "helpful" `--no-verify` bypass on a failing pre-commit hook ships a secret or broken build.

## Guardrails

- Do not use destructive operations as a shortcut past an obstacle — find the root cause instead (see `no-silent-failures`, `systematic-debugging`).
- Never skip hooks or bypass signing to make a blocker go away without the user explicitly asking for it.
- When in doubt about reversibility, treat it as irreversible.
