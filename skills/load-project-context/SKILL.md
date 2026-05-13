---
name: load-project-context
description: Use before planning, editing, fixing, refactoring, documenting, reviewing, or completing work in any project area that uses spec-driven-development.
---

# Load Project Context

Load only the active working context needed for the next task. Historical plans,
decision records, run logs, and archives are reference material; search or read
them only when the task asks for history or clearly revisits a previous choice.

## Workflow

1. **Read `AREAS.md` at the project root.** It lists every area with its `Path:` and `Docs:` entries plus a short prose description.
2. **Identify the touched area(s).** Match the user's prompt and any quoted file paths against the `Path:` entries in `AREAS.md`. If multiple areas are involved, load each one.
3. **Read the area's `<docsRoot>/README.md` first.** It states the area's purpose and the doc reading order.
4. **Load the active docs only:**
   - `implementation-status.md` — always; it should be a compact current snapshot.
   - `spec.md`, `product-spec.md`, or the active `specs/<date>-<feature>.md` named by the README/status — when the work touches behavior, scope, schema, architecture, UX, or acceptance.
   - `operations.md` or another runbook — only when the work involves running, deploying, importing, exporting, retrying, or troubleshooting the area.
   - `acceptance-criteria.md` — only when the area keeps criteria separate from the spec and the task changes or verifies those criteria.
   - `plans/<date>-<batch>.md` — only when continuing or modifying that in-flight batch.
5. **Avoid history by default.**
   - Do not read `decisions/`, `logs/`, `archive/`, or old plans during normal coding unless the README/status points to a specific current record or the task asks why something was chosen.
   - If you suspect a previous decision matters, use targeted search first. Search only history paths that exist, e.g. build a short candidate list from `<docsRoot>/decisions`, `<docsRoot>/logs`, and `<docsRoot>/archive`, then run `rg -n "<topic>" ...` and read only the matching file.
   - For onboarding, broad history review, or "did we already decide this?", use `review-project-history` instead of loading history into working context.
6. **State which areas and docs you used.** Before drafting a plan or making edits, list them in your response so the human partner can confirm coverage.
7. **Seed a final task-list/checklist item** using the host's task tool: "Run `maintain-project-docs` before claiming completion." Use `TodoWrite` in Claude Code, `update_plan` in Codex, or the local checklist mechanism in other agents.

## When to skip

Skip the workflow only when the user asks a pure question with no planned edits, explicitly asks to skip the plugin, or the current directory is not a project repository.

## If `AREAS.md` is missing

Stop and ask the human partner whether to scaffold one. Offer to copy `templates/AREAS.md` from the plugin and seed it with the obvious areas detected in the repo. Do not invent areas silently.
