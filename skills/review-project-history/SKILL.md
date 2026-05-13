---
name: review-project-history
description: Use when onboarding to a project area, researching why a choice was made, or checking whether earlier plans, decisions, logs, or archives already cover a topic.
---

# Review Project History

Retrieve historical context deliberately, then summarize only what matters for the
current question. This skill is the escape hatch for rich context; normal coding
work should use `load-project-context` instead.

## Workflow

1. **Read `AREAS.md` at the project root.** Match the prompt and paths to the relevant area entries.
2. **Read the area's active entry points first:**
   - `<docsRoot>/README.md`
   - `<docsRoot>/implementation-status.md`, if present
   - the active `spec.md`, `product-spec.md`, or named feature spec, if present
3. **Search history before reading it.** Build a candidate list from history paths that actually exist, then run targeted search over that list. Likely paths include:
   - `<docsRoot>/decisions/`
   - `<docsRoot>/logs/`
   - `<docsRoot>/archive/`
   - `<docsRoot>/plans/`
   - `<docsRoot>/specs/`
4. **Read only relevant matches.** Prefer one decision record or one archived plan over loading a whole folder. Do not pass missing paths to search commands. If matches are broad, summarize headings first and choose the smallest useful subset.
5. **Return a concise history summary:**
   - settled decisions that still apply;
   - rejected alternatives and why they were rejected;
   - stale or superseded material;
   - open questions that remain unresolved;
   - files worth reading next, if any.
6. **Do not update docs from this skill unless the user asks.** If history should be promoted into active docs, recommend the smallest specific doc change.

## When To Use

Use this for prompts like:
- "onboard me to this area";
- "why did we choose this?";
- "did we already consider this?";
- "review the old plans before we change it";
- "summarize the decision history."

Do not use it for routine coding context.
