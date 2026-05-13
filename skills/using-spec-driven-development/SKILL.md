---
name: using-spec-driven-development
description: Use when starting, planning, editing, fixing, refactoring, documenting, reviewing, or completing project work in a repository that uses spec-driven-development.
---

# Using Spec-Driven Development

Keep compact active docs in the loop for project work. Treat historical docs as
retrieval material, not default coding context.

## Workflow

1. Decide whether the request is project work. Project work includes code, docs, config, tests, schemas, behavior, UX, architecture, repo structure, or completion review.
2. Skip only when the user asks a pure question with no planned edits, explicitly asks to skip the plugin, or the current directory is not a project repository.
3. Before planning or editing, use `load-project-context`. If `AREAS.md` is missing, stop and ask whether to scaffold it from `templates/AREAS.md`, unless the user only asked for inspection.
4. Add a final checklist item: "Run `maintain-project-docs` before claiming completion." Use `update_plan` in Codex, `TodoWrite` in Claude Code, or the local checklist mechanism in other agents.
5. Use `review-project-history` instead of `load-project-context` when the user asks to onboard, research prior choices, or review old decisions/plans/logs.
6. Use `update-docs-structure` when the user asks to migrate older docs to the latest plugin structure.
7. Before claiming completion, use `maintain-project-docs` for every touched area.
8. In the final reply, name the area docs read and updated, or explain why no docs changed.

## Priority

User and repository instructions outrank this skill. If instructions conflict, state which instruction controls and continue under the higher-priority instruction.
