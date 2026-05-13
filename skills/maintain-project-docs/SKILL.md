---
name: maintain-project-docs
description: Use before claiming project-area work complete after editing, fixing, refactoring, documenting, reviewing, or updating files.
---

# Maintain Project Docs

Update the touched area's active docs in the same batch as the code. Keep active
docs small. Historical decisions, verification logs, and archived plans should be
created or linked only when they add durable retrieval value.

## Workflow

1. **Identify the touched area(s).** Cross-check the files changed against the `Path:` entries in `AREAS.md`.
2. **Open each touched area's `implementation-status.md`, if present.** Keep it to current state, next checkpoint, known gaps, active plan pointers, and latest verification. Move older verification detail to `logs/verification.md` when it starts to dominate the file.
3. **Update the active spec only when the contract changed.** Use `spec.md`, `product-spec.md`, or the active `specs/<date>-<feature>.md`, depending on the area's convention. Keep acceptance checks inside the spec unless the area intentionally maintains a separate `acceptance-criteria.md`.
4. **Update `acceptance-criteria.md` only when it exists and is actively used.** It is optional; do not create it for small areas when spec-local acceptance checks are enough.
5. **Update `operations.md` only for operational behavior.** Examples: commands, environment variables, paid-call gates, deploy/import/export steps, retry paths, and stop conditions.
6. **Add durable decisions without loading full history.**
   - Prefer creating a new file under `decisions/YYYY-MM-DD-short-title.md` using the decision-record template.
   - Update `decisions/README.md` only as a short index, if the area has one.
   - Do not append long chronological entries to an active working doc.
7. **Update `README.md`** when the reading order, area focus, active spec pointer, or docs layout changed.
8. **Use the no-doc-change escape hatch only when appropriate.** If code changed but the area contract, current state, operations, acceptance checks, and decisions did not change, do not make a noisy docs edit. Instead, include `Docs unchanged by design: <reason>` in the final response.
9. **If an expected active doc is missing**, propose creating it from `templates/`. Do not silently skip.
10. **Confirm in your reply** which docs were updated, or give the `Docs unchanged by design:` reason. Only then claim the work is done.

## What counts as a durable decision

A decision belongs in a decision record when:
- It rules out an alternative approach future contributors might revisit.
- It commits the area to a contract (schema shape, endpoint pattern, library choice).
- It encodes a trade-off the code alone cannot explain.

Reversing a decision later is fine; add a new decision record and update the index
instead of rewriting history.
