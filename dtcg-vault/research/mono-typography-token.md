---
language_protocol: DESIGN-TOKEN-CHANGE
master_cosign: operator, 2026-09-16 — approved directly in conversation with Command,
  in response to project-construction's proposal (msg-id
  project-construction-20260916-iteration-n-2-fast-complete-2-items-flag /
  project-construction-20260916-comprehensive-outstanding-items-sweep-fo)
---

# Design-token change: `typography.mono` primitive

## What changed

Added `typography.mono` (`--ps-primitive-typography-mono`) to
`dtcg-vault/tokens/primitive.json` — a general-purpose monospace typography
composite, companion to the existing `typography.wiki-code` (also IBM Plex
Mono) and the platform's already-shipped IBM Plex Sans usage in the
`wiki-*` family.

## Why

`app-privategit-construction` (project-construction's archive) already
self-hosts IBM Plex Mono locally (`render::DESIGN_TOKENS`, Version 3 Phase 6),
paired with its own self-hosted IBM Plex Sans, for cost codes, work-package
IDs, command-composer syntax, and tabular numeric columns — with real
positive results reported. Before this change, IBM Plex Mono existed in
`pointsav-design-system` only inside the wiki-specific `wiki-code` token, not
as a general-purpose primitive other consuming apps could reference. This
promotes it to a durable shared token, following the platform's convention
of consolidating repeated per-app choices into the shared design system once
proven (same rationale used for prior token promotions).

## Values

Font stack matches project-construction's own real, already-deployed
`--mono` custom property exactly (`ui-monospace, 'SFMono-Regular', 'SF Mono',
Menlo, Consolas, 'DejaVu Sans Mono', 'Liberation Mono', monospace` fallback
chain), so no visual change for that app if/when it migrates to the shared
token. Size/weight/line-height (0.9375rem/400/1.5) match `wiki-code`'s own
values for consistency within this design system, adjusted only for a
slightly more generous line-height suited to tabular/general-purpose use
rather than inline wiki code specifically.

## Downstream impact

Additive only — no existing token's value changed. `wiki-code` is untouched.
Consuming apps opt in by referencing the new token; nothing is forced to
migrate.

## Research trail

- research_done_count: 1 (this file)
- research_suggested_count: 0
- open_questions_count: 0
- research_provenance: project-construction's own `render::DESIGN_TOKENS`
  source (`app-privategit-construction/src/render/mod.rs`), read directly
  this session, not assumed from the proposal's prose alone.
- research_inline: font-stack and size values quoted verbatim above from the
  real source.
