# spec-driven-development — Contributor Notes

These notes apply to anyone (human or agent) editing **this plugin repo**. They do not apply to projects that consume the plugin.

## Scope

The plugin is generic. It must contain no references to specific projects, products, codebases, employers, or domains. The only allowed exceptions are inside `docs/specs/` design notes, which are historical context.

## What the plugin ships

- Five skills: `using-spec-driven-development`, `load-project-context`, `maintain-project-docs`, `review-project-history`, and `update-docs-structure`.
- Three hooks: `SessionStart`, `UserPromptSubmit`, and `Stop`.
- Markdown templates under `templates/`.
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

- `bash -n hooks/session-start`, `bash -n hooks/user-prompt-submit`, and `bash -n hooks/stop` must pass.
- Run `hooks/session-start` from a throwaway repo with `AREAS.md` and from a directory without `AREAS.md`; `bash -n` does not catch every runtime quoting issue.
- Run `hooks/user-prompt-submit` once with a project-work prompt and once with a non-project prompt.
- Run `hooks/stop` once in a throwaway repo with code-only changes and once after adding a docs change.
- JSON manifests must parse.

## What does not belong here

- Project-specific area names, URLs, or workflows.
- Skills or hooks that only help one team.
- Test scaffolding for the plugin itself; the plugin is markdown plus shell.
