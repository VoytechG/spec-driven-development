# Release Notes

## v0.1.0 (2026-04-26)

Initial release.

- Two skills: `load-project-context` (fires before planning or editing) and `maintain-project-docs` (fires before completion).
- Two Claude-compatible hooks: `SessionStart` surfaces `AREAS.md`; `UserPromptSubmit` seeds task-list reminders on substantive prompts.
- Eight markdown templates: `AREAS.md`, `area-readme.md`, `product-spec.md`, `acceptance-criteria.md`, `implementation-status.md`, `decisions.md`, `feature-spec.md`, `implementation-plan.md`.
- Multi-area example at `examples/AREAS.md`.
- Platform manifests for Claude Code, Codex, and Gemini CLI.
- Polyglot `hooks/run-hook.cmd` launcher for Windows + Unix.
- Marketplace logo at `assets/icon-fantasy-map.png`.
