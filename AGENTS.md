# spec-driven-development — Contributor Notes

These notes apply to anyone (human or agent) editing **this plugin repo**. They do not apply to projects that consume the plugin.

## Scope

The plugin is generic. It must contain no references to specific projects, products, codebases, employers, or domains. The only allowed exceptions are inside `docs/specs/` design notes, which are historical context.

## What the plugin ships

- Two skills: `load-project-context` and `maintain-project-docs`.
- Two hooks: `SessionStart` and `UserPromptSubmit`.
- Eight markdown templates under `templates/`.
- Three platform manifests: Claude Code, Codex, Gemini CLI.
- One marketplace logo under `assets/`.

Every change should leave this list intact unless the change deliberately adds or removes one of these pieces.

## Style

- Tight, specific prose. The plugin sells concise documentation; practice it here too.
- No emojis in markdown content.
- Hook scripts: `set -euo pipefail`, `printf` (not heredoc), shellcheck-clean, extensionless filenames.
- Skill `SKILL.md`: YAML frontmatter with `name` and `description` only; imperative-mood body; no padding.
- Templates: section headers plus a one-line italic hint per section. No placeholder paragraphs.

## Versioning

- Bump `version` in all four manifests together: `package.json`, `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.codex-plugin/plugin.json`, `gemini-extension.json`.
- Add a `RELEASE-NOTES.md` entry per release.

## Local checks

- `bash -n hooks/session-start` and `bash -n hooks/user-prompt-submit` must pass.
- JSON manifests must parse.

## What does not belong here

- Project-specific area names, URLs, or workflows.
- Skills or hooks that only help one team.
- Test scaffolding for the plugin itself; the plugin is markdown plus shell.
