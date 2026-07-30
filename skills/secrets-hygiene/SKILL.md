---
name: secrets-hygiene
description: Use before any git add/commit/push, and when writing files that might contain credentials. Prevents secrets from leaving the machine.
version: 1.0.0
---

# Secrets Hygiene

**Iron law: NOTHING WITH A KEY, TOKEN, OR CREDENTIAL LEAVES THE MACHINE UNREVIEWED.**

## Usage

- `/secrets-hygiene`
- Triggered before: `git add -A`/`git add .` or any broad stage, `git commit`, `git push`, creating a new file that stores config/credentials, or uploading content to any third-party service (gist, pastebin, design tool, issue tracker).

## Checklist

1. **Never stage broadly.** Prefer adding specific files by name. If `-A`/`.` was already used, run `git status` and inspect every new/modified file before committing.
2. **Grep staged diffs for secret shapes** before commit: `git diff --cached | grep -iE '(api[_-]?key|secret|token|password|bearer|-----BEGIN)'`. A hit doesn't always mean a real secret, but always requires a look.
3. **Check filenames, not just content.** `.env`, `credentials.json`, `*.pem`, `id_rsa*`, `*.key` are secret-shaped regardless of content — treat as blocked unless explicitly `.gitignore`d and confirmed safe.
4. **Innocuous-looking files can still contain secrets.** Config files, test fixtures, and notebook outputs are common accidental leak vectors — a filename that looks fine doesn't clear the content.
5. **If a secret is found in history (not just the pending commit)**, do not just delete it in a new commit — it's still in git history. Stop and flag to the user; this needs `git filter-repo`/BFG or a rotated credential, not a normal fix.
6. **Third-party uploads (gists, pastebins, diagram tools) are effectively public** even if marked private/unlisted — treat any content going there as no longer confidential once uploaded.

## Failure scenarios this prevents

- `git add -A` after a debugging session accidentally stages a `.env` file that was created for local testing.
- A "helpful" commit includes a config file with a live API key because the diff was never read before commit.
- A secret gets removed in a follow-up commit but remains permanently recoverable in git history.
- Debug output or a config snippet pasted into a gist for sharing purposes turns out to contain a bearer token.

## Guardrails

- Do not commit .env-shaped or key-shaped files even if the user says "just add everything" — flag it and ask instead of complying silently (this is a case where confirming intent matters more than speed).
- False positives are cheap to check and expensive to ignore — when in doubt, open the file and look.
