# spec-driven-development — Claude Code Notes

Read `AGENTS.md` first. This file adds Claude-specific points only.

## Manifest

Claude Code reads `.claude-plugin/plugin.json` and the marketplace listing in `.claude-plugin/marketplace.json`. Keep both in sync with `package.json`.

## Hook output shape

The `hooks/session-start` script emits Claude's `hookSpecificOutput.additionalContext` shape when `CLAUDE_PLUGIN_ROOT` is set and `COPILOT_CLI` is not. Do not change the shape without testing on Claude Code; Claude reads both `additional_context` and `hookSpecificOutput` and would double-inject.

## Skill discovery

Skills live under `skills/<name>/SKILL.md` with YAML frontmatter (`name`, `description`). Claude Code's Skill tool surfaces them by name. The description is the trigger phrase the agent matches against — keep it as a one-line "use when ..." cue.
