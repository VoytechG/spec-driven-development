---
name: maintain-project-docs
description: Use before claiming project-area work complete after editing, fixing, refactoring, documenting, reviewing, or updating files.
---

# Maintain Project Docs

Update the touched area's docs in the same batch as the code. Do not claim completion until the docs reflect the change or the gap is documented.

## Workflow

1. **Identify the touched area(s).** Cross-check the files changed against the `Path:` entries in `AREAS.md`.
2. **Open each touched area's `implementation-status.md`.** Move items between `Delivered`, `In progress`, `Deferred`, and `Known gaps` to reflect what just shipped. Add a `Verification` note describing what was checked and how.
3. **Open each touched area's `decisions.md`.** If the batch made a durable decision a future contributor would otherwise have to reverse-engineer from code, add an entry: `## YYYY-MM-DD: <title>` with one-line statement, `Reason:`, and `Implications:`.
4. **Update `acceptance-criteria.md`** when a criterion was met (move to a "Satisfied" subsection, do not delete) or added.
5. **Update `product-spec.md`** when scope, principles, or workflows changed.
6. **Update `README.md`** when the reading order, north star, or area focus shifted.
7. **If a required doc is missing**, propose creating it from `templates/`. Do not silently skip.
8. **Confirm in your reply** which docs were updated. Only then claim the work is done.

## What counts as a durable decision

A decision belongs in `decisions.md` when:
- It rules out an alternative approach future contributors might revisit.
- It commits the area to a contract (schema shape, endpoint pattern, library choice).
- It encodes a trade-off the code alone cannot explain.

Reversing a decision later is fine — log a new entry rather than editing the old one.
