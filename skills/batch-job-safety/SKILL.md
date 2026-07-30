---
name: batch-job-safety
description: Use when designing or running any job that processes many items (ETL, migrations, bulk API calls, mass file edits). Ensures partial failure is safe, resumable, and visible.
version: 1.0.0
---

# Batch Job Safety

**Iron law: A BATCH JOB MUST SURVIVE BEING KILLED HALFWAY THROUGH.**

## Usage

- `/batch-job-safety`
- Triggered before writing or running any loop/job that: processes N>1 items, calls an external API/DB repeatedly, or performs destructive/irreversible per-item actions (writes, deletes, sends).

## Pre-flight checklist

1. **Idempotency** — running the job twice on the same input must not double-apply effects (dedupe by ID, upsert not insert, check-before-write).
2. **Checkpointing** — the job records progress (last processed ID/offset) somewhere durable, so a crash resumes instead of restarting from zero.
3. **Partial-failure isolation** — one item's failure does not abort the whole batch, but is also not silently dropped (see `no-silent-failures`). Collect failures into an explicit list surfaced at the end.
4. **Dry run first** — for anything destructive or irreversible, run against a sample or with a `--dry-run` flag and inspect output before the real run.
5. **Rate/blast-radius limits** — cap concurrency and per-run item count for anything hitting shared/external systems, especially on the first real run.
6. **Reversibility check** — before running, confirm: can this be undone? If not, get explicit confirmation before executing (see risk-of-irreversible-action judgment).

## Failure scenarios this prevents

- A migration script killed at item 4,000/10,000 with no checkpoint — re-running reprocesses and double-applies the first 4,000.
- A bulk-email/notification job that crashes mid-run and, on restart, resends to everyone instead of resuming from the failure point.
- A mass file-edit script that dies partway, leaving the repo in an inconsistent half-migrated state with no record of which files were touched.

## Guardrails

- Don't build checkpointing/resumability infrastructure for a one-off script processing a handful of items — this applies once item count or external-system risk is nontrivial.
- Dry-run output must be inspected, not just produced — running `--dry-run` and immediately running for real without reading the diff defeats the purpose.
