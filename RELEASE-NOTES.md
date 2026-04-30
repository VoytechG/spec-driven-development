# Release Notes

## v0.2.0 (2026-04-29)

Codex auto-coordination release.

- Added `using-spec-driven-development`, a coordinator skill that can trigger on project work and route agents through the load/update skills.
- Updated `load-project-context` and `maintain-project-docs` so small project changes are covered by the same docs loop.
- Reworded hook reminders to "project work" and broadened the prompt heuristic.
- Marked the Codex plugin interface as interactive because missing `AREAS.md` setup can require confirmation.
- Added a Codex-native marketplace catalog at `.agents/plugins/marketplace.json`.

## v0.1.0 (2026-04-26)

Initial release.

- Two skills: `load-project-context` (fires before planning or editing) and `maintain-project-docs` (fires before completion).
- Two Claude-compatible hooks: `SessionStart` surfaces `AREAS.md`; `UserPromptSubmit` seeds task-list reminders on project-work prompts.
- Eight markdown templates: `AREAS.md`, `area-readme.md`, `product-spec.md`, `acceptance-criteria.md`, `implementation-status.md`, `decisions.md`, `feature-spec.md`, `implementation-plan.md`.
- Multi-area example at `examples/AREAS.md`.
- Platform manifests for Claude Code, Codex, and Gemini CLI.
- Polyglot `hooks/run-hook.cmd` launcher for Windows + Unix.
- Marketplace logo at `assets/icon-fantasy-map.png`.
