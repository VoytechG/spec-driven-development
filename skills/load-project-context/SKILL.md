---
name: load-project-context
description: Use before planning, editing, fixing, refactoring, documenting, reviewing, or completing work in any project area that uses spec-driven-development.
---

# Load Project Context

Load the right area's documentation before drafting plans, making edits, or claiming completion. Apply this to all project work, including small changes.

## Workflow

1. **Read `AREAS.md` at the project root.** It lists every area with its `Path:` and `Docs:` entries plus a short prose description.
2. **Identify the touched area(s).** Match the user's prompt and any quoted file paths against the `Path:` entries in `AREAS.md`. If multiple areas are involved, load each one.
3. **Read the area's `<docsRoot>/README.md` first.** It states the area's purpose and the doc reading order.
4. **Then read the rest of the doc set as relevance dictates:**
   - `product-spec.md` — when the work touches user-facing behavior or scope.
   - `acceptance-criteria.md` — when the work claims to satisfy a criterion.
   - `implementation-status.md` — always; this is the current state.
   - `decisions.md` — when the work touches an area covered by an earlier decision.
   - `specs/<date>-<feature>.md` — when the work is part of a named feature.
   - `plans/<date>-<batch>.md` — when continuing or modifying an in-flight batch.
5. **State which areas and docs you used.** Before drafting a plan or making edits, list them in your response so the human partner can confirm coverage.
6. **Seed a final task-list/checklist item** using the host's task tool: "Run `maintain-project-docs` before claiming completion." Use `TodoWrite` in Claude Code, `update_plan` in Codex, or the local checklist mechanism in other agents.

## When to skip

Skip the workflow only when the user asks a pure question with no planned edits, explicitly asks to skip the plugin, or the current directory is not a project repository.

## If `AREAS.md` is missing

Stop and ask the human partner whether to scaffold one. Offer to copy `templates/AREAS.md` from the plugin and seed it with the obvious areas detected in the repo. Do not invent areas silently.
