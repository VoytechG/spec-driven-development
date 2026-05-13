---
name: update-docs-structure
description: Use when migrating an existing project's spec-driven docs to the latest compact active-docs plus historical-retrieval structure.
---

# Update Docs Structure

Migrate existing area docs to the current plugin structure without losing history
or loading unnecessary archival detail. This skill is for docs-layout upgrades,
not routine feature work.

## Workflow

1. **Read `AREAS.md` at the project root.** Identify the area or areas named by the prompt.
2. **Inventory before reading.**
   - List files under each area's docs root.
   - Check sizes with `wc -l`.
   - Scan headings with `rg -n "^#|^##|^###"`.
   - Read full files only when the inventory shows they are active docs or need migration.
3. **Classify docs by purpose.**
   - Active: `README.md`, `spec.md` or `product-spec.md`, `implementation-status.md`, active feature specs, active plans.
   - Operations: `operations.md` or existing runbooks with commands, env, deploy/import/export, retries, or stop conditions.
   - Decisions: `decisions/` records plus a short `decisions/README.md` index.
   - History: `logs/`, shipped plans, old run reviews, verification history, import manifests, and archives.
4. **Propose the target shape before large rewrites.** For small updates, proceed directly. For large or ambiguous doc sets, summarize the migration map and ask before moving content.
5. **Migrate and prune deliberately.**
   - Keep active docs compact.
   - Move old verification/run detail to `logs/`.
   - Convert long chronological decisions into atomic decision records plus a short index when practical.
   - Move shipped or superseded plans/specs into `archive/` when their active contract has a replacement.
   - Prune old-format docs such as monolithic `decisions.md`, bloated status ledgers, duplicated acceptance files, and obsolete plans after preserving needed history and updating links.
   - Remove old-path files completely once active references are updated. Do not keep compatibility shims or pointer files unless the user explicitly asks for backwards compatibility.
   - Preserve source material; do not delete history unless explicitly requested.
6. **Use the latest structure.**
   - Acceptance checks live in the active spec unless a separate checklist is clearly useful.
   - `operations.md` exists only for real runbook needs.
   - `implementation-status.md` is a current snapshot: current state, next checkpoint, active plan, deferred work, known gaps, latest verification.
   - Decision detail lives in `decisions/YYYY-MM-DD-short-title.md`; indexes stay short.
7. **Check references before pruning.**
   - Use `rg` to find links and prose references to files being removed or archived.
   - Update active-doc reading order, runbook indexes, and in-repo references.
   - Report any remaining external-link risk explicitly.
8. **Verify.**
   - Run `git diff --check`.
   - Re-scan the docs root to confirm active docs are small and history is discoverable.
   - Report which docs were read, changed, archived, pruned, or intentionally left alone.

## Stop Conditions

Stop and ask before proceeding when:
- moving files would break links and there is no obvious redirect/index update;
- a long doc mixes active contract with historical evidence and the correct split is unclear;
- a decision entry lacks enough date/title/context to split safely;
- the user asked for review only, not edits.
