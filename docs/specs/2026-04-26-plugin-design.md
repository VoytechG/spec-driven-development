# Spec: spec-driven-development plugin

Date: 2026-04-26
Status: Historical; updated with v0.4 context-discipline changes on 2026-05-13
Authors: VoytechG

## 2026-05-13 v0.4 Update

The original v0.1 design below treated a fixed seven-file docs package as the default context surface. The v0.4 design keeps the same core goal, but separates **active working context** from **historical retrieval context**:

- Normal coding uses compact active docs only: `README.md`, `implementation-status.md`, and the active `spec.md` / `product-spec.md` or named feature spec.
- `acceptance-criteria.md` is optional. Small areas should keep acceptance checks inside the active spec.
- `operations.md` is optional and only for runbooks: commands, environment variables, deploy/import/export steps, retries, paid-call gates, and stop conditions.
- Durable decisions should live as atomic `decisions/YYYY-MM-DD-short-title.md` records with a short `decisions/README.md` index. Old top-level `decisions.md` files should be removed during structure migration unless a project explicitly asks for compatibility shims.
- Old decisions, verification logs, run history, and shipped plans are historical context. They are not loaded during normal coding.
- `review-project-history` is the dedicated skill for onboarding, "why did we choose this?", and targeted old-plan/log/decision research.
- `update-docs-structure` is the dedicated skill for migrating older docs packages into the current compact active-docs plus historical-retrieval layout.
- The Stop hook still blocks code-only completions with no docs, but accepts an explicit final-response escape hatch: `Docs unchanged by design: <reason>`.

Read the rest of this file as original design history. The current operational contract is in `README.md`, the skill files, templates, and release notes.

## Goal

Enable spec-driven development across one or many project areas in a repository.

Spec-driven development means: every project area carries compact active docs for execution and separate historical docs for retrieval. Code follows the active spec; specs describe intent in natural language; both stay in sync as a single atomic work batch.

The benefits, when this discipline holds:

- **Instant onboarding.** A fresh session — human or agent — finds active docs and a deliberate history-review path. No chat-history reconstruction.
- **Acceptance criteria in plain English.** Every feature has checks a non-technical reviewer can read and verify. "Done" lives in prose, not only in test names.
- **Concise project context for any development work.** Every change starts by loading a small, predictable set of active docs scoped to the area being touched. No bloated context, no missing context.

The plugin enforces the documentation shape via a coordinator skill, four focused skills, and lifecycle hook scripts. Humans and agents read the same files. Across project areas — main app, prototype, sub-module — the shape stays identical, so context-switching feels the same as switching files within one area.

This plugin is **complementary to [`obra/superpowers`](https://github.com/obra/superpowers)**: superpowers gives agents *workflows* (brainstorming, TDD, debugging, planning); spec-driven-development gives them *project context*. Both can run side-by-side.

## What context means

For a normal coding session to pick up work effectively, an agent or developer needs:

| Need | Doc that holds it |
|------|-------------------|
| What this area is and how to navigate its docs | `README.md` |
| What we are building, for whom, why, and how to verify it | `spec.md`, `product-spec.md`, or active `specs/<date>-<feature>.md` |
| Current state, next checkpoint, active plan, and known gaps | `implementation-status.md` |
| Commands, env, deploy/import/export, retries, and stop conditions | optional `operations.md` |
| The plan for an in-flight work batch | active `plans/<date>-<batch>.md` |
| Why earlier decisions were made | `review-project-history` over `decisions/`, `logs/`, and `archive/` |

Each active file has one job. Together they answer: *what, why, where are we, and what's next.* Historical files answer *what was already decided* only when the task needs that retrieval.

## What each document does

Modeled on the doc set in `gqi-portal/prototypes/custom-kit/docs/`, which has proven to work for a real prototype.

### `README.md` — entry point

The one file a fresh session should read first. Contains:

- One-paragraph purpose of the area.
- Reading order for the rest of the doc set.
- A short "north star" — the principle behind the area's design.
- A maintenance rule reminding contributors to update docs in the same batch as code.

Updated when the focus or scope shifts.

### `spec.md` or `product-spec.md` — what we are building and why

The active contract. Small areas can use `spec.md`; product-heavy areas can keep `product-spec.md`. Contains:

- Goal of the area in product terms.
- Users / consumers of what is being built.
- Product principles that guide trade-offs.
- Core workflows (the user journeys the area must support).
- Scope and non-goals.
- Acceptance checks, unless the area maintains a separate large checklist.

Updated when product direction changes.

### `acceptance-criteria.md` — the "done" bar

Optional. Use this file only when the acceptance matrix is large, cross-release, or maintained separately from the active spec.

For small areas, keep acceptance checks inside the active spec.

### `implementation-status.md` — current state

Compact current state of the area. Sections:

- **Current state** — what is implemented and usable now.
- **Next checkpoint** — the next concrete task.
- **Active plan** — current specs/plans or "None".
- **Deferred** — what was intentionally pushed out, with reason.
- **Known gaps** — gaps the team is aware of but has not addressed.
- **Latest verification** — newest check only, with a link to `logs/verification.md` if detailed history matters.

Updated when current state changes. Do not use this as a diary.

### `decisions/` — the why log

One durable choice per file:

```markdown
# Decision Record — <Short Title>

Date: YYYY-MM-DD
Status: Active | Superseded | Reversed

## Decision
...

## Alternatives considered
...
```

`decisions/README.md` may exist as a short index. Normal coding should not read the full decision history; use `review-project-history` for targeted retrieval.

### `specs/` — feature designs

One file per feature, dated: `specs/2026-04-24-hybrid-crud-entity-workspace.md`. Contains:

- Goal of the feature.
- Scope and non-goals.
- Architecture / approach.
- Acceptance criteria for that feature.
- Open questions.

Lives while the feature is active. After that it is historical context — kept, not loaded by default.

### `plans/` — implementation plans

One file per work batch, dated: `plans/2026-04-24-crud-first-workspace.md`. Contains:

- The ordered list of changes.
- Files touched.
- Verification steps.
- Anything intentionally deferred from the plan.

Lives while the batch is active. After that it is historical context — kept, not loaded by default.

### `operations.md` and optional / project-specific docs

Use `operations.md` only where an area needs a runbook: commands, environment variables, deploy/import/export steps, retries, paid-call gates, or stop conditions.

The plugin does not require these, but encourages them where they fit:

- `schema-model.md` — for data-heavy areas.
- `ui-workspaces.md` — for UI-heavy areas.
- `architecture.md` — when the area's structure deserves its own page.

These are project judgement calls. The plugin requires only the active docs an area actually needs.

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
3. Read the matched area's `<docsRoot>/README.md` first.
4. Load compact active docs only: `implementation-status.md`, active spec, optional operations docs for operational tasks, and active plans when continuing that plan.
5. Avoid `decisions/`, logs, archives, and old plans by default.
6. State the areas + docs used in the response before drafting a plan or making edits.
7. Seed a final task-list/checklist item using the host's task tool: "Run `maintain-project-docs` before claiming completion." Use `TodoWrite` in Claude Code, `update_plan` in Codex, or the local checklist mechanism in other agents.

Skips only for pure questions with no planned edits, explicit user opt-out, or non-project contexts.

### `maintain-project-docs`

Fires before claiming completion. Workflow:

1. Identify which area(s) were touched.
2. Update compact active docs only when state, contract, operations, acceptance, or active plan pointers changed.
3. Create atomic decision records for durable decisions instead of appending long chronological logs.
4. Use `Docs unchanged by design: <reason>` for pure refactors or equivalent changes where docs should not change.
5. If an expected active doc is missing for a touched area, propose creating it from the shipped templates.
6. Do not claim done until docs are updated, a gap is documented, or the no-doc-change escape hatch is stated.

### `review-project-history`

Fires for onboarding, "why did we choose this?", "did we already decide this?", and old-plan/log/archive review. Workflow:

1. Read `AREAS.md` and the area's active entry points.
2. Build a candidate list from history paths that exist.
3. Use targeted search before reading history.
4. Read only relevant decision records, old plans, logs, or archive files.
5. Summarize settled decisions, rejected alternatives, stale material, unresolved questions, and next files to read.

### `update-docs-structure`

Fires when migrating an existing docs package to the latest plugin structure. Workflow:

1. Read `AREAS.md` and identify the target area.
2. Inventory docs with file lists, line counts, and heading scans before reading large files.
3. Classify docs as active, operations, decisions, or history.
4. Propose a migration map before large rewrites.
5. Keep active docs compact, preserve historical material, split long decisions into records when practical, and prune old-format files after updating in-repo references.
6. Verify with `git diff --check` and a post-migration inventory.

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
- Invoke `review-project-history` for onboarding, old decisions, logs, archives, or why-this-was-chosen research.
- Invoke `update-docs-structure` when migrating older area docs to the latest plugin structure.

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
1. Load <area> active docs via load-project-context skill
2. Use review-project-history only for onboarding or old decision/log/archive research
3. Use update-docs-structure for migrations to the latest docs layout
4. Update <area> docs at end via maintain-project-docs skill
```

### `Stop`

When `AREAS.md` exists and the git worktree has changed non-doc project files but no changed documentation files, blocks final completion with a reminder to run `maintain-project-docs`. If the worktree is clean, it also checks the most recent commit when it is fresh so code-only commits made during the session are still guarded. For pure refactors or equivalent changes, the final response can include `Docs unchanged by design: <reason>` to satisfy the guard without noisy docs edits. The hook exits silently when it sees `stop_hook_active` to avoid loops.

## Cross-platform support

Three target platforms, three plugin manifests, same docs and skills where the host supports skill discovery. Hook wiring is explicit per platform:

| Platform     | Manifest                                                | Context file | v0.4 behavior |
|--------------|---------------------------------------------------------|--------------|---------------|
| Claude Code  | `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` | `CLAUDE.md`  | Skills plus `SessionStart`, `UserPromptSubmit`, and `Stop` hooks through `hooks/hooks.json`. |
| Codex        | `.codex-plugin/plugin.json` (with `interface` block)    | `AGENTS.md`  | Skills, coordinator skill, marketplace metadata/logo, and explicit lifecycle hook wiring through `./hooks/hooks.json`. |
| Gemini CLI   | `gemini-extension.json`                                 | `GEMINI.md`  | Extension context plus templates/skills used directly; no hook events wired. |

Hook scripts use the polyglot `hooks/run-hook.cmd` launcher pattern from `obra/superpowers` so a single extensionless script per behavior runs on both Windows and Unix. `SessionStart` and `UserPromptSubmit` emit `hookSpecificOutput.additionalContext` by default for Claude Code and Codex, `additional_context` for Cursor, and top-level `additionalContext` for Copilot. `Stop` emits Codex/Claude-style stop JSON only when it needs to block completion.

## Templates shipped with the plugin

`templates/` directory:

- `AREAS.md` — example project-root index.
- `area-readme.md` — starter README for a new area.
- `spec.md` — compact active spec.
- `product-spec.md` — starter product spec.
- `acceptance-criteria.md` — optional acceptance criteria.
- `implementation-status.md` — starter status doc.
- `operations.md` — optional operations runbook.
- `decisions-readme.md` — optional short decision index, copied to `decisions/README.md`.
- `decision-record.md` — atomic decision record.
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
    review-project-history/
      SKILL.md
    update-docs-structure/
      SKILL.md

  templates/
    AREAS.md
    area-readme.md
    spec.md
    product-spec.md
    acceptance-criteria.md
    implementation-status.md
    operations.md
    decisions-readme.md
    decision-record.md
    feature-spec.md
    implementation-plan.md

  examples/
    AREAS.md                    # fuller, multi-area example

  assets/
    icon-fantasy-map.png        # marketplace logo
    icon.png                    # simple fallback icon
```

## Historical Migration Plan For gqi-portal

This was the v0.1 adoption plan and is retained as history, not current installation guidance:

1. Write `AREAS.md` at gqi-portal root, declaring the four areas: `main`, `graph-backend-kit`, `custom-kit`, `directus-kit`. Include the integration-intent prose for each prototype.
2. Backfill missing files in `directus-kit` and `graph-backend-kit` with the active docs required by the then-current template set.
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
- [ ] `load-project-context` skill triggers on project-work prompts and loads the right area's active docs without history by default.
- [ ] `maintain-project-docs` skill triggers before completion claim.
- [ ] `review-project-history` skill is available and uses targeted search for old decisions, logs, archives, and shipped plans.
- [ ] `update-docs-structure` skill is available and migrates old docs packages through inventory, classification, pruning of replaced old-format files, and verification.
- [ ] `Stop` hook accepts `Docs unchanged by design: <reason>` for pure refactors or equivalent no-doc-change completions.
- [ ] Project with one area passes; project with five areas passes; missing `AREAS.md` falls back gracefully.
- [ ] gqi-portal can adopt the plugin and replace the in-repo version end-to-end.
- [ ] `README.md` install instructions are tested for the Claude Code path at minimum.
- [ ] `LICENSE` (MIT) is present.
- [ ] Repo has a tagged release on GitHub.
