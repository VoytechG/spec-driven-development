# Project Areas

This file is the registry of project areas read by the spec-driven-development plugin.
One section per area. Hook-capable hosts can surface this file as session context.

## main

- Path: `.`
- Docs: `docs/project`

The production-facing application. Source of truth for shipped behavior. Other areas in this repo feed ideas, schemas, or UI patterns into `main` once they prove themselves.

## backend-experiment

- Path: `prototypes/backend-experiment`
- Docs: `prototypes/backend-experiment/docs`

Backend-only prototype exploring an alternative data layer. Not user-facing. If the architecture proves out, the data-layer ideas integrate into the main app's backend; the prototype itself does not ship.

## admin-prototype

- Path: `prototypes/admin-prototype`
- Docs: `prototypes/admin-prototype/docs`

Schema-driven admin UI prototype. If successful, integrates as the main app's admin layer. Carries its own product spec because the admin audience differs from the main app's end users.

## vendor-evaluation

- Path: `prototypes/vendor-evaluation`
- Docs: `prototypes/vendor-evaluation/docs`

Off-the-shelf alternative being evaluated against `admin-prototype`. The doc set here captures evaluation criteria, observed gaps, and the decision record. Likely retired once the build-vs-buy call is made.
