# spec-driven-development — Gemini CLI Notes

Read `AGENTS.md` first. This file adds Gemini-specific points only.

## Manifest

Gemini CLI reads `gemini-extension.json` at the repo root. The `contextFileName` field points to this file. Keep `version` synced with the other manifests.

## Context

Gemini surfaces the file named by `contextFileName` to every session. Keep it short and stable; long-lived context is read on every turn.

## Hooks

The packaged hook scripts are portable, but `gemini-extension.json` does not wire hook events. Use the skills and templates directly in Gemini sessions.
