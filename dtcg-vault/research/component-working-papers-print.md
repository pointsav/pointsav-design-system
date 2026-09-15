# Research — Working Papers Print Stylesheet

This file records why each decision in the `working-papers-print` component
was made. A generation agent building or modifying a `/working-papers` page
on any of the 6 JOURNAL-publishing sites should read this before touching
print behavior.

## 1. Why one shared file instead of 6 site-specific print stylesheets

Each of the 6 sites (`gis.woodfinegroup.com`, `bim.woodfinegroup.com`,
`design.pointsav.com`, `software.pointsav.com`, `home.pointsav.com`,
`home.woodfinegroup.com`) renders `/working-papers` with its own native
templating stack — deliberately; there is no shared rendering engine. But a
reader printing a paper from any of the 6 sites should get visually
consistent output. Six independently-authored print stylesheets would drift
the moment one site's author tweaked margins or forgot a rule the others
have. One file, adopted verbatim everywhere, makes drift structurally
impossible rather than something to audit for.

**Codegen rule:** when a site needs a print-behavior change, change this
file and have every adopting site re-pull it. Never fork a per-site copy.

## 2. Relationship to `financial-report-layout`

`financial-report-layout` (this vault) already solved several of the same
print-CSS problems — page setup, background-fill preservation, cross-engine
table-layout re-resolution — for compliance financial documents rendered via
WeasyPrint. `working-papers-print` reuses those *lessons*, not that file's
CSS wholesale: JOURNAL papers are prose-with-occasional-tables rendered via
a browser print engine (`window.print()`), not dense multi-year financial
tables rendered server-side. Concretely shared: the `break-inside`/`break-
after` avoidance pattern, the "restate table-layout inside `@media print`"
gotcha, and the general shape of a forced-page-break escape hatch
(`.page-break-before`/`.page-break-after`, same class names, same mechanism).
Concretely different: page orientation (letter portrait here vs. letter
landscape there — JOURNAL papers are not column-dense), and there is no
line-number gutter (that is a financial-statement convention, not a JOURNAL
one).

**Codegen rule:** do not point a JOURNAL page at `financial-report-layout`'s
CSS, and do not point a compliance financial document at this file — they
are siblings solving adjacent problems, not one generalizing the other.

## 3. Why `print-color-adjust: exact` on the universal selector

Browsers strip background colors and non-default text colors by default
when printing, on the theory that saving ink is more valuable than fidelity.
For a JOURNAL page this silently degrades any color-coded content (a
callout fill, the masthead rule) with no visible warning to the author. This
is the single most commonly-missed print-CSS requirement across this
workspace's own print recipes (see `financial-report-layout` research §
"why `print-color-adjust:exact` is required for gutter and fills to survive
print" for the same finding in a different component).

**Codegen rule:** never remove this rule to "simplify" the stylesheet; its
absence is a silent, hard-to-notice regression, not a visible one.

## 4. Why the chrome-hiding selector list is broad, not per-site

`nav`, `footer`, and `[class*="nav"|"breadcrumb"|"footer"|"theme-
toggle"|"lang-switch"|"search"]` covers common naming across the 6 sites'
independently-built templating stacks without requiring this file to know
any one site's actual class names. This trades slight over-inclusiveness
(a false-positive match hiding something that happens to contain "nav" in
its class name but isn't navigation) for zero per-site maintenance — the
same tradeoff `financial-report-layout`'s `table.wide` group-alignment rule
makes in the opposite direction (precision over portability, because that
component is authored once per document, not shared verbatim across 6
codebases).

**Codegen rule:** if a real chrome element survives on some site after
applying this file, widen the shared selector list. Do not add a per-site
override block — see §1.

## 5. Why `a[href^="http"]::after { content: "" }` is a deliberate no-op

JOURNAL body text carries zero outbound links by editorial rule (JOURNAL
master BRIEF Decision #5) — enforced upstream in the editorial pipeline, not
by this stylesheet. This rule exists purely as a defensive hook: if a
footnote-style URL ever legitimately appears in a citation entry, there is
already a place to add print-visible link text without re-deriving the
selector. It renders nothing today because there is nothing to render.

**Codegen rule:** do not delete this rule as "dead code" — it is a
deliberately pre-placed hook, not an oversight. Do not populate its
`content` value without confirming the zero-outbound-link editorial rule has
actually changed.

## 6. Why `letter` portrait, not landscape

Financial-report-layout uses letter *landscape* because it needs to fit a
label column plus up to 11 period columns side by side. A JOURNAL working
paper is prose read top to bottom with occasional single tables or figures —
letter *portrait* is the standard reading orientation and matches how every
other prose document on these 6 sites already prints.

**Codegen rule:** do not switch a `/working-papers` page to landscape to fit
a wide table; if a paper genuinely needs a wide table, that table should use
`financial-report-layout`'s `table.wide` pattern scoped to just that
element, not force the whole document to landscape.

## Research trail

### Done (5)
- Confirmed the print CSS already landed (2026-09-15, project-editorial's
  `/working-papers` scaffold commit) as an inline block in
  `app-privategit-design/static/portal.css`, self-labeled in its own comment
  header as a standalone `working-papers-print.css` file and as "Owned by
  project-design" — extracted verbatim into this vault as the canonical,
  shared source rather than left as a single site's local copy.
- Verified against `financial-report-layout`'s research file for shared
  mechanism vs. genuinely different content shape (see §2) rather than
  assuming either "identical" or "unrelated."
- Verified `schema-guide.yaml`/`schema-topic.yaml` (the unrelated
  `journal_refs` DESIGN-TOKEN-CHANGE reviewed the same session) do not
  reference or depend on this component in any way — no cross-draft
  coupling to account for.
- Confirmed zero-outbound-link editorial rule (JOURNAL master BRIEF
  Decision #5) as the reason for §5's no-op rule, rather than treating it as
  unexplained dead code.
- Confirmed all 6 target sites render `/working-papers` with independent
  native templating (no shared engine) — the reason this must be a single
  portable CSS file rather than a token baked into any one site's build.

### Suggested (2)
- Once a second site (beyond `design.pointsav.com`) actually adopts this
  file, verify the broad chrome-hiding selector list in §4 against that
  site's real markup and widen it if anything survives — untested against
  any site but `design.pointsav.com` as of this writing.
- Confirm the `table.wide`-in-a-JOURNAL-page escape hatch mentioned in §6
  (scoping a wide table locally rather than forcing landscape) with a real
  example once a paper actually needs one — currently a design intention,
  not yet exercised by real content.

### Open questions (1)
- Should this file also cover a non-print PDF-export path (e.g. a "Download
  PDF" button using a headless-browser print-to-PDF service) for sites that
  want one, or does relying on the reader's own browser print dialog stay
  the permanent, only mechanism? No JOURNAL site has asked for a download
  button yet; not deciding this until one does.
