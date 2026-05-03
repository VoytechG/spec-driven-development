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

A repo-root `AREAS.md` file lists the areas. Skills load the right area's docs before edits and update them before completion. On Codex, the `using-spec-driven-development` coordinator skill gives the plugin a broad project-work trigger and routes agents through the focused load/update skills. Lifecycle hooks also surface `AREAS.md`, seed task-list reminders, and guard completion when project files changed without docs.

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
codex plugin marketplace add VoytechG/spec-driven-development --ref v0.3.1
```

Then restart Codex, open `/plugins`, choose the `Spec-Driven Development` marketplace, and install `spec-driven-development`.

### Gemini CLI

```bash
gemini extensions install https://github.com/VoytechG/spec-driven-development
```

## Quick start

1. Copy `templates/AREAS.md` to your repo root and edit it: one `## ` heading per area, with `Path:` and `Docs:` lines plus a short prose description.
2. For each area, copy the rest of `templates/` into the area's `Docs:` directory and fill them in.
3. Restart your session. For project work, the `using-spec-driven-development` skill should route the agent through `load-project-context` before edits and `maintain-project-docs` before completion. In Codex and Claude Code, lifecycle hooks also read `AREAS.md`, seed reminders, and check for missing doc updates before final completion.

## Platform support

- Claude Code: skills plus `SessionStart`, `UserPromptSubmit`, and `Stop` hooks through `hooks/hooks.json`.
- Codex: marketplace metadata, skills, marketplace logo, and explicit lifecycle hook wiring through `.codex-plugin/plugin.json`.
- Gemini CLI: extension context through `GEMINI.md`; use the skills and templates directly.

Hook scripts use the same portable `run-hook.cmd` pattern as Superpowers, so other hook-capable hosts can wire them without changing the scripts.

## What ships

- `skills/using-spec-driven-development/` — coordinator skill for project work; routes agents through the load/update skills.
- `skills/load-project-context/` — fires before planning, editing, or completion review; reads `AREAS.md` and the matching area's docs.
- `skills/maintain-project-docs/` — fires before completion; updates `implementation-status.md` and `decisions.md` for touched areas.
- `hooks/session-start` — surfaces `AREAS.md` at session start on hook-capable hosts; falls back to a friendly prompt if missing.
- `hooks/user-prompt-submit` — seeds task-list reminders on hook-capable hosts when the prompt looks like project work.
- `hooks/stop` — blocks final completion when changed project files are present but docs did not change.
- `templates/` — eight starter markdown files.
- `examples/AREAS.md` — multi-area example.
- `assets/icon-fantasy-map.png` — marketplace logo.

## License

MIT, see `LICENSE`.
