# spec-driven-development — Claude Code Notes

Read `AGENTS.md` first. This file adds Claude-specific points only.

## Manifest

Claude Code reads `.claude-plugin/plugin.json` and the marketplace listing in `.claude-plugin/marketplace.json`. Keep both in sync with `package.json`.

## Hook output shape

The `hooks/session-start` and `hooks/user-prompt-submit` scripts emit Claude/Codex `hookSpecificOutput.additionalContext` by default, Cursor `additional_context` when `CURSOR_PLUGIN_ROOT` is set, and Copilot's older top-level `additionalContext` when `COPILOT_CLI` is set. Do not emit multiple shapes from one hook; hosts can double-inject.

## Skill discovery

Skills live under `skills/<name>/SKILL.md` with YAML frontmatter (`name`, `description`). Claude Code's Skill tool surfaces them by name. The description is the trigger phrase the agent matches against — keep it as a one-line "use when ..." cue.
