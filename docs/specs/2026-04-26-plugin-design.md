# Spec: spec-driven-development plugin

Date: 2026-04-26
Status: Draft (awaiting review)
Authors: VoytechG

## Goal

Enable spec-driven development across one or many project areas in a repository.

Spec-driven development means: every project area carries a small, standard documentation set — product spec, acceptance criteria, implementation status, decisions, plus per-feature specs and per-batch plans. Code follows the spec; specs describe intent in natural language; both stay in sync as a single atomic work batch.

The benefits, when this discipline holds:

- **Instant onboarding.** A fresh session — human or agent — finds the same docs in the same place across every area. No archaeology, no chat-history reconstruction.
- **Acceptance criteria in plain English.** Every feature has a checklist a non-technical reviewer can read and verify. "Done" lives in prose, not only in test names.
- **Concise project context for any development work.** Every change starts by loading a small, predictable set of docs scoped to the area being touched. No bloated context, no missing context.

The plugin enforces the documentation shape via a coordinator skill, two focused skills, and lifecycle hook scripts. Humans and agents read the same files. Across project areas — main app, prototype, sub-module — the shape stays identical, so context-switching feels the same as switching files within one area.

This plugin is **complementary to [`obra/superpowers`](https://github.com/obra/superpowers)**: superpowers gives agents *workflows* (brainstorming, TDD, debugging, planning); spec-driven-development gives them *project context*. Both can run side-by-side.

## What context means

For a session to pick up work effectively, an agent or developer needs:

| Need | Doc that holds it |
|------|-------------------|
| What this area is and how to navigate its docs | `README.md` |
| What we are building, for whom, and why | `product-spec.md` |
| The concrete checklist of "done" | `acceptance-criteria.md` |
| What is delivered, in progress, deferred, or a known gap | `implementation-status.md` |
| Why earlier decisions were made (so we don't relitigate them) | `decisions.md` |
| The design of a specific feature | `specs/<date>-<feature>.md` |
| The plan for a specific work batch | `plans/<date>-<batch>.md` |

Each file has one job. Together they answer: *what, why, where are we, what's next, and what was already decided.* That is enough to start contributing on day one.

## What each document does

Modeled on the doc set in `gqi-portal/prototypes/custom-kit/docs/`, which has proven to work for a real prototype.

### `README.md` — entry point

The one file a fresh session should read first. Contains:

- One-paragraph purpose of the area.
- Reading order for the rest of the doc set.
- A short "north star" — the principle behind the area's design.
- A maintenance rule reminding contributors to update docs in the same batch as code.

Updated when the focus or scope shifts.

### `product-spec.md` — what we are building and why

The "what & why" doc. Contains:

- Goal of the area in product terms.
- Users / consumers of what is being built.
- Product principles that guide trade-offs.
- Core workflows (the user journeys the area must support).
- Scope and non-goals.

Updated when product direction changes.

### `acceptance-criteria.md` — the "done" bar

A checklist in plain English. Each item describes a behavior the area must satisfy, concrete enough to verify manually or by test, but readable by a non-technical reviewer. "Done" lives here, in prose — not only in test names.

Updated when the acceptance bar shifts (e.g. new requirement) or when criteria are met (move them to a "satisfied" subsection rather than deleting).

### `implementation-status.md` — current state

Living state of the area. Sections:

- **Delivered** — what is implemented and verified.
- **In progress** — what is currently being worked on.
- **Deferred** — what was intentionally pushed out, with reason.
- **Known gaps** — gaps the team is aware of but has not addressed.
- **Verification** — what was checked, and how.

Updated every work batch. This is the most frequently touched doc.

### `decisions.md` — the why log

One entry per durable decision, in chronological order:

```markdown
## YYYY-MM-DD: <short title>

<one-line decision statement>

Reason:
- ...

Implications:
- ...
```

Decisions belong here when a future contributor would otherwise have to reverse-engineer the reasoning from code. Reversing decisions later is fine — log a new entry instead of editing the old one.

### `specs/` — feature designs

One file per feature, dated: `specs/2026-04-24-hybrid-crud-entity-workspace.md`. Contains:

- Goal of the feature.
- Scope and non-goals.
- Architecture / approach.
- Acceptance criteria for that feature.
- Open questions.

Lives until the feature ships. After that it is historical context — kept, not deleted.

### `plans/` — implementation plans

One file per work batch, dated: `plans/2026-04-24-crud-first-workspace.md`. Contains:

- The ordered list of changes.
- Files touched.
- Verification steps.
- Anything intentionally deferred from the plan.

Lives until the batch ships. After that it is historical context — kept, not deleted.

### Optional / project-specific docs

The plugin does not require these, but encourages them where they fit:

- `schema-model.md` — for data-heavy areas.
- `ui-workspaces.md` — for UI-heavy areas.
- `architecture.md` — when the area's structure deserves its own page.

These are project judgement calls. The plugin only requires the seven core ones above.

## Project-level config: `AREAS.md`

Configuration is text, not JSON. Same shape used by `obra/superpowers` philosophy and the auto-memory system: humans and agents read the same file.

A project using this plugin has one file at its repo root: **`AREAS.md`**. Format:

```markdown
# Project Areas

This file is the registry of project areas read by the spec-driven-development plugin.
One section per area. Hook-capable hosts can read this file and surface it as context.

## main

- Path: `.`
- Docs: `docs/project`

The main GQI Portal app — the production-facing product.

## graph-backend-kit

- Path: `prototypes/graph-backend-kit`
- Docs: `prototypes/graph-backend-kit/docs`

Backend-only experiment exploring schema-driven graph architecture in Postgres + Bun.
If the pattern proves itself, the data layer ideas integrate into the main app's backend.

## custom-kit

- Path: `prototypes/custom-kit`
- Docs: `prototypes/custom-kit/docs`

Schema-driven admin prototype: TypeScript schema → generated REST + React UI.
If successful, integrates as the main app's admin / entity workspace layer.

## directus-kit

- Path: `prototypes/directus-kit`
- Docs: `prototypes/directus-kit/docs`

Off-the-shelf alternative — evaluating Directus as the main app's admin foundation.
```

That is the entire config surface. No JSON, no schema. Skills read `AREAS.md` directly; hook-capable hosts can also surface its content as session context. Project-specific concepts (integration intent, status, ownership) live as prose in each area's section. Adding a new area is one new `## ` heading.

If `AREAS.md` is missing, the `SessionStart` hook says so and points at the project's docs.

## Skills

### `using-spec-driven-development`

Fires when starting, planning, editing, fixing, refactoring, documenting, reviewing, or completing project work in a repo using this plugin. Workflow:

1. Decide whether the request is project work.
2. Before planning or editing, invoke `load-project-context`.
3. Seed a final task-list/checklist item: "Run `maintain-project-docs` before claiming completion."
4. Before claiming completion, invoke `maintain-project-docs`.
5. Report which docs were read and updated.

This coordinator gives Codex and other skill-aware hosts a broad project-work trigger. Lifecycle hooks add session context and guardrails, but the coordinator remains the primary route into the focused load/update skills.

### `load-project-context`

Fires before planning or editing. Workflow:

1. Read `AREAS.md` (project root).
2. Identify which area(s) the work touches, from the prompt and any quoted paths.
3. Read the matched area's `<docsRoot>/README.md` first, then the rest of the doc set as relevance dictates.
4. State the areas + docs used in the response before drafting a plan or making edits.
5. Seed a final task-list/checklist item using the host's task tool: "Run `maintain-project-docs` before claiming completion." Use `TodoWrite` in Claude Code, `update_plan` in Codex, or the local checklist mechanism in other agents.

Skips only for pure questions with no planned edits, explicit user opt-out, or non-project contexts.

### `maintain-project-docs`

Fires before claiming completion. Workflow:

1. Identify which area(s) were touched.
2. Open each touched area's `implementation-status.md` and `decisions.md`; update if behavior, schema, or decisions changed in the work batch.
3. If a required doc is missing for a touched area, propose creating it from the shipped templates.
4. Do not claim done until the docs are updated or the gap is documented.

## Hooks

### `SessionStart`

Reads `AREAS.md` if present and dumps its content as session context, prefixed with a usage reminder and an instruction-priority block:

```
This project uses the spec-driven-development plugin.

# Project Areas
... (contents of AREAS.md) ...

Skills available:
- Invoke `load-project-context` before planning or editing in any area.
- Invoke `maintain-project-docs` before claiming completion.

Instruction priority:
1. User instructions (CLAUDE.md, AGENTS.md, GEMINI.md, direct chat, project memory) — highest.
2. spec-driven-development plugin skills and prompts.

If a user instruction conflicts with this plugin's guidance, follow the user.
```

The priority block follows the pattern from `obra/superpowers`'s `using-superpowers/SKILL.md`: the plugin's own context tells the agent that user instructions outrank the plugin's prompts. No orchestration logic — pure prompt engineering.

If `AREAS.md` is missing, emits a friendly prompt: "No AREAS.md found at project root. Add one (see plugin README) or copy from `templates/AREAS.md`."

### `UserPromptSubmit`

Emits a task-list/checklist reminder when the prompt looks like project work (heuristic regex match on keywords like *implement, build, edit, update, fix, refactor, schema, route, design, spec, docs*). Stays silent on prompts that do not appear to involve project changes.

```
For project work in any registered area, seed task-list/checklist items:
1. Load <area> docs via load-project-context skill
2. Update <area> docs at end via maintain-project-docs skill
```

### `Stop`

When `AREAS.md` exists and the git worktree has changed non-doc project files but no changed documentation files, blocks final completion with a reminder to run `maintain-project-docs` or explain why docs do not change. If the worktree is clean, it also checks the most recent commit when it is fresh so code-only commits made during the session are still guarded. The hook exits silently when it sees `stop_hook_active` to avoid loops.

## Cross-platform support

Three target platforms, three plugin manifests, same docs and skills where the host supports skill discovery. Hook wiring is explicit per platform:

| Platform     | Manifest                                                | Context file | v0.3 behavior |
|--------------|---------------------------------------------------------|--------------|---------------|
| Claude Code  | `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` | `CLAUDE.md`  | Skills plus `SessionStart`, `UserPromptSubmit`, and `Stop` hooks through `hooks/hooks.json`. |
| Codex        | `.codex-plugin/plugin.json` (with `interface` block)    | `AGENTS.md`  | Skills, coordinator skill, marketplace metadata/logo, and explicit lifecycle hook wiring through `./hooks/hooks.json`. |
| Gemini CLI   | `gemini-extension.json`                                 | `GEMINI.md`  | Extension context plus templates/skills used directly; no hook events wired. |

Hook scripts use the polyglot `hooks/run-hook.cmd` launcher pattern from `obra/superpowers` so a single extensionless script per behavior runs on both Windows and Unix. `SessionStart` and `UserPromptSubmit` emit `hookSpecificOutput.additionalContext` by default for Claude Code and Codex, `additional_context` for Cursor, and top-level `additionalContext` for Copilot. `Stop` emits Codex/Claude-style stop JSON only when it needs to block completion.

## Templates shipped with the plugin

`templates/` directory:

- `AREAS.md` — example project-root index.
- `area-readme.md` — starter README for a new area.
- `product-spec.md` — starter product spec.
- `acceptance-criteria.md` — starter acceptance criteria.
- `implementation-status.md` — starter status doc.
- `decisions.md` — starter decisions log.
- `feature-spec.md` — starter spec for `specs/`.
- `implementation-plan.md` — starter plan for `plans/`.

Templates are plain markdown. A project copies them via prompt or manually. The plugin does not auto-create.

## Plugin file layout

```
spec-driven-development/
  .claude-plugin/
    plugin.json
    marketplace.json
  .codex-plugin/
    plugin.json
  AGENTS.md
  CLAUDE.md
  GEMINI.md
  gemini-extension.json
  package.json
  LICENSE                       # MIT
  README.md
  RELEASE-NOTES.md

  hooks/
    hooks.json
    run-hook.cmd                # polyglot Win/Unix launcher (from superpowers)
    session-start               # extensionless hook script
    user-prompt-submit          # extensionless hook script
    stop                        # extensionless hook script

  skills/
    using-spec-driven-development/
      SKILL.md
    load-project-context/
      SKILL.md
    maintain-project-docs/
      SKILL.md

  templates/
    AREAS.md
    area-readme.md
    product-spec.md
    acceptance-criteria.md
    implementation-status.md
    decisions.md
    feature-spec.md
    implementation-plan.md

  examples/
    AREAS.md                    # fuller, multi-area example

  assets/
    icon-fantasy-map.png        # marketplace logo
    icon.png                    # simple fallback icon
```

## Migration plan for gqi-portal

Once v0.1 of this plugin ships:

1. Write `AREAS.md` at gqi-portal root, declaring the four areas: `main`, `graph-backend-kit`, `custom-kit`, `directus-kit`. Include the integration-intent prose for each prototype.
2. Backfill missing files in `directus-kit` (currently has only `experiment-notes.md`) and `graph-backend-kit` (no `decisions.md`, no `acceptance-criteria.md`) so each area has at least the seven core docs.
3. Replace the in-repo `plugins/gqi-spec-driven-development/` with the marketplace-installed `spec-driven-development`.
4. Update `AGENTS.md` and `docs/project/README.md` to reference the new plugin.

## Open questions

1. **Marketplace registration.** Claude Code official marketplace registration takes a manual review step. Plan: ship via a self-hosted marketplace first (analogous to `obra/superpowers-marketplace`), apply to the official marketplace after dogfooding stabilizes.
2. **Codex sync.** Superpowers maintains a `sync-to-codex-plugin.sh` script that mirrors itself into `prime-radiant-inc/openai-codex-plugins`. Defer for v0.1; add later if Codex marketplace adoption requires it.
3. **Path-aware hook output.** Currently the hook is static (lists all areas always). Could become path-aware in v0.2. Defer.

## Acceptance criteria

- [ ] Plugin installs in Claude Code, Codex, and Gemini CLI without errors.
- [ ] Claude Code and Codex `SessionStart` hooks read `AREAS.md` and surface its content correctly.
- [ ] Claude Code and Codex `UserPromptSubmit` hooks seed project-work reminders.
- [ ] Claude Code and Codex `Stop` hooks block completion when project files changed but docs did not.
- [ ] Codex marketplace entry uses the fantasy-map logo and declares lifecycle hook wiring.
- [ ] `using-spec-driven-development` skill triggers on project-work prompts and routes through the load/update skills.
- [ ] `load-project-context` skill triggers on project-work prompts and loads the right area's docs.
- [ ] `maintain-project-docs` skill triggers before completion claim.
- [ ] Project with one area passes; project with five areas passes; missing `AREAS.md` falls back gracefully.
- [ ] gqi-portal can adopt the plugin and replace the in-repo version end-to-end.
- [ ] `README.md` install instructions are tested for the Claude Code path at minimum.
- [ ] `LICENSE` (MIT) is present.
- [ ] Repo has at least one tagged release on GitHub (`v0.1.0`).
