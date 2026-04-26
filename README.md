# spec-driven-development

A small plugin that gives every project area the same documentation set, so a fresh session — human or agent — finds the same files in the same place every time.

## What it does

For each project area (the main app, a prototype, a sub-module), the plugin expects a fixed doc set:

- `README.md` — entry point and reading order.
- `product-spec.md` — what is being built, for whom, why.
- `acceptance-criteria.md` — plain-English checklist of "done".
- `implementation-status.md` — delivered, in progress, deferred, known gaps, verification.
- `decisions.md` — chronological log of durable decisions.
- `specs/<date>-<feature>.md` — per-feature designs.
- `plans/<date>-<batch>.md` — per-batch implementation plans.

A repo-root `AREAS.md` file lists the areas. Skills load the right area's docs before edits and update them before completion. On hook-capable hosts, the shipped hooks can also surface `AREAS.md` and task-list reminders automatically.

The plugin is complementary to workflow plugins like [`obra/superpowers`](https://github.com/obra/superpowers): superpowers gives agents *workflows*; this plugin gives them *project context*.

## Install

### Claude Code

```bash
/plugin marketplace add VoytechG/spec-driven-development
/plugin install spec-driven-development@spec-driven-development
```

### Codex

In the Codex CLI:

```bash
/plugins
```

Search for `spec-driven-development` and install.

### Gemini CLI

```bash
gemini extensions install https://github.com/VoytechG/spec-driven-development
```

## Quick start

1. Copy `templates/AREAS.md` to your repo root and edit it: one `## ` heading per area, with `Path:` and `Docs:` lines plus a short prose description.
2. For each area, copy the rest of `templates/` into the area's `Docs:` directory and fill them in.
3. Restart your session. Use the `load-project-context` skill before edits. In Claude Code, the `SessionStart` hook also reads `AREAS.md` and surfaces it automatically.

## Platform support

- Claude Code: skills plus `SessionStart` and `UserPromptSubmit` hooks through `hooks/hooks.json`.
- Codex: marketplace metadata, skills, and marketplace logo. Task-list reminders live in the skills because the Codex manifest does not wire hook events.
- Gemini CLI: extension context through `GEMINI.md`; use the skills and templates directly.

Hook scripts use the same portable `run-hook.cmd` pattern as Superpowers, so other hook-capable hosts can wire them without changing the scripts.

## What ships

- `skills/load-project-context/` — fires before planning or editing; reads `AREAS.md` and the matching area's docs.
- `skills/maintain-project-docs/` — fires before completion; updates `implementation-status.md` and `decisions.md` for touched areas.
- `hooks/session-start` — surfaces `AREAS.md` at session start on hook-capable hosts; falls back to a friendly prompt if missing.
- `hooks/user-prompt-submit` — seeds task-list reminders on hook-capable hosts when the prompt looks like substantive work.
- `templates/` — eight starter markdown files.
- `examples/AREAS.md` — multi-area example.
- `assets/icon-fantasy-map.png` — marketplace logo.

## License

MIT, see `LICENSE`.
