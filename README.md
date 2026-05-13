# spec-driven-development

A small plugin that gives every project area compact active docs plus an explicit path for historical context, so a fresh session — human or agent — loads what is useful for the task instead of every detail ever written.

## What it does

For each project area (the main app, a prototype, a sub-module), the plugin expects a small active doc set:

- `README.md` — entry point and reading order.
- `spec.md` or `product-spec.md` — what is being built, for whom, why, and how to verify it.
- `implementation-status.md` — current state, active plan pointer, known gaps, and latest verification only.
- `operations.md` — optional runbook for commands, environment, deploy/import/export, retries, and stop conditions.
- `specs/<date>-<feature>.md` — optional per-feature designs when a compact area spec is not enough.
- `plans/<date>-<batch>.md` — optional in-flight implementation plans.

Historical context is separate:

- `decisions/YYYY-MM-DD-short-title.md` — one durable choice per file.
- `decisions/README.md` — optional short index, not a long chronological log.
- `logs/` and `archive/` — verification history, run history, old plans, and evidence.

A repo-root `AREAS.md` file lists the areas. Skills load the right area's docs before edits and update them before completion. On Codex, the `using-spec-driven-development` coordinator skill gives the plugin a broad project-work trigger and routes agents through the focused load/update skills. Lifecycle hooks also surface `AREAS.md`, seed task-list reminders, and guard completion when project files changed without docs.

When code changes do not affect docs, agents can finish with an explicit escape hatch: `Docs unchanged by design: <reason>`. The Stop hook treats that as an auditable no-doc-change acknowledgement rather than forcing noisy status edits.

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
codex plugin marketplace add VoytechG/spec-driven-development --ref v0.4.0
```

Then restart Codex, open `/plugins`, choose the `Spec-Driven Development` marketplace, and install `spec-driven-development`.

### Gemini CLI

```bash
gemini extensions install https://github.com/VoytechG/spec-driven-development
```

## Quick start

1. Copy `templates/AREAS.md` to your repo root and edit it: one `## ` heading per area, with `Path:` and `Docs:` lines plus a short prose description.
2. For each area, copy the active templates you need into the area's `Docs:` directory and fill them in. Start with `area-readme.md`, either `spec.md` or `product-spec.md`, and `implementation-status.md`; add `operations.md`, separate acceptance criteria, plans, or decision records only when they earn their keep.
3. Restart your session. For project work, the `using-spec-driven-development` skill should route the agent through `load-project-context` before edits and `maintain-project-docs` before completion. Use `review-project-history` only for onboarding, decision research, or old-plan review. In Codex and Claude Code, lifecycle hooks also read `AREAS.md`, seed reminders, and check for missing doc updates before final completion.

## Platform support

- Claude Code: skills plus `SessionStart`, `UserPromptSubmit`, and `Stop` hooks through `hooks/hooks.json`.
- Codex: marketplace metadata, skills, marketplace logo, and explicit lifecycle hook wiring through `.codex-plugin/plugin.json`.
- Gemini CLI: extension context through `GEMINI.md`; use the skills and templates directly.

Hook scripts use the same portable `run-hook.cmd` pattern as Superpowers, so other hook-capable hosts can wire them without changing the scripts.

## What ships

- `skills/using-spec-driven-development/` — coordinator skill for project work; routes agents through the load/update/history skills.
- `skills/load-project-context/` — fires before planning, editing, or completion review; reads `AREAS.md` and compact active docs only.
- `skills/maintain-project-docs/` — fires before completion; updates active docs and creates small decision records when needed.
- `skills/review-project-history/` — retrieves decisions, logs, archives, and old plans for onboarding or targeted history research.
- `skills/update-docs-structure/` — migrates and prunes older area docs to the compact active-docs plus historical-retrieval structure.
- `hooks/session-start` — surfaces `AREAS.md` at session start on hook-capable hosts; falls back to a friendly prompt if missing.
- `hooks/user-prompt-submit` — seeds task-list reminders on hook-capable hosts when the prompt looks like project work.
- `hooks/stop` — blocks final completion when changed project files are present but docs did not change.
- `templates/` — starter markdown files for active docs, optional runbooks, plans, specs, and decision records.
- `examples/AREAS.md` — multi-area example.
- `assets/icon-fantasy-map.png` — marketplace logo.

## License

MIT, see `LICENSE`.
