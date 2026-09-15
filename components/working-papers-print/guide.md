# Working Papers — Print Stylesheet

A shared, portable `@media print` stylesheet for the `/working-papers` (JOURNAL)
series. One file, adopted verbatim by all 6 sites that publish JOURNAL content
(`gis.woodfinegroup.com`, `bim.woodfinegroup.com`, `design.pointsav.com`,
`software.pointsav.com`, `home.pointsav.com`, `home.woodfinegroup.com`). Each
site renders `/working-papers` with its own native templating (no shared
rendering engine — that is deliberate), but print output must look identical
across all 6, so the print CSS itself is one shared asset rather than 6
independently-invented stylesheets.

Mechanism is browser-native: `window.print()` + `@media print` / `@page`
CSS. No server-side PDF generation. Adapted from the real, print-tested
techniques already proven in this vault's `financial-report-layout` recipe
(`dtcg-vault/research/component-financial-report-layout.md`) — that recipe is
for dense financial tables rendered via WeasyPrint; this one is for JOURNAL
prose/figure/occasional-table content rendered via a browser print engine.
Same underlying gotchas (backgrounds stripped by default, table layout
re-resolved at print time), different content shape and rendering path.

## When to use

Link this file on every `/working-papers` page, in addition to the site's own
normal stylesheet. It only takes effect inside `@media print` — it does not
affect on-screen rendering and requires no other integration. Do not fork a
per-site copy; if a real site-chrome element survives after applying this
file, widen the selector list below instead of maintaining a separate copy.

Do not use this for compliance financial documents (tables, multi-year
statements) — use `financial-report-layout` for those; the two recipes are
siblings, not interchangeable.

## Complete CSS

```css
/* working-papers-print.css
 * Shared browser-native print stylesheet for the /working-papers series,
 * identical across all 6 sites. Owned by project-design.
 *
 * Usage: link this file on every /working-papers page, in addition to the
 * site's own normal stylesheet. It only takes effect inside @media print —
 * it does not affect on-screen rendering and requires no other integration.
 */

@media print {
  /* Real, load-bearing: without this, browsers strip background fills and
     colors by default when printing, silently degrading any color-coded
     content (the paper's tables, callouts, the masthead rule). This is the
     single most commonly-missed print-CSS requirement. */
  * {
    -webkit-print-color-adjust: exact;
    print-color-adjust: exact;
    color-adjust: exact;
  }

  @page {
    size: letter;
    margin: 0.75in 0.85in;
  }

  /* Hide everything that is site chrome, not the paper itself. Each site's
     own markup uses different class names for nav/footer/search/theme-toggle
     — this list is deliberately broad (attribute + common-name matching)
     rather than tied to one site's naming, so the same file works everywhere
     without per-site editing. Add a site-specific selector here only if a
     real chrome element survives after applying this file; do not maintain
     a separate per-site copy for this. */
  nav,
  footer,
  [class*="nav"],
  [class*="breadcrumb"],
  [class*="footer"],
  [class*="theme-toggle"],
  [class*="lang-switch"],
  [class*="search"],
  .no-print,
  .working-papers-print-hide {
    display: none !important;
  }

  body,
  main,
  article,
  .working-papers-print-root {
    margin: 0 !important;
    padding: 0 !important;
    max-width: none !important;
    background: #fff !important;
    color: #000 !important;
  }

  /* A working paper is read start to finish; a heading stranded at the
     bottom of a page, orphaned from the section it introduces, is the
     single most common print defect. Keep headings glued to what follows,
     and keep tables/blockquotes/figures from splitting mid-block. */
  h1, h2, h3, h4, h5, h6 {
    break-after: avoid;
    page-break-after: avoid;
  }

  table, blockquote, figure, pre, .working-papers-print-keep {
    break-inside: avoid;
    page-break-inside: avoid;
  }

  /* Some print engines re-resolve table layout at print time and silently
     drop rules that are not re-asserted inside @media print itself — this
     is a documented, non-obvious cross-engine gotcha (see
     financial-report-layout's own recipe notes). Any /working-papers page
     using a fixed table layout on screen must restate it here explicitly;
     this generic rule covers the common case of a two-column claims/tests
     table.
  */
  table {
    table-layout: fixed;
    width: 100%;
    border-collapse: collapse;
  }
  table th, table td {
    border: 1px solid #999;
    padding: 4px 6px;
    font-size: 10pt;
  }

  /* Deliberate manual escape hatches for a paper's own markup, e.g. forcing
     the rigor block or an appendix to start on its own page. Never applied
     automatically — the paper's own HTML opts in per element. */
  .page-break-before {
    break-before: page;
    page-break-before: always;
  }
  .page-break-after {
    break-after: page;
    page-break-after: always;
  }

  a[href] {
    color: #000 !important;
    text-decoration: none !important;
  }
  /* Working papers carry zero outbound links by editorial rule (JOURNAL
     master BRIEF Decision #5) — this rule is defensive, not load-bearing,
     in case a footnote-style URL ever appears in a citation entry. */
  a[href^="http"]::after {
    content: "";
  }

  body {
    font-size: 11pt;
    line-height: 1.4;
  }
}
```

## Markup expectations

No required markup beyond ordinary semantic HTML. The stylesheet degrades
gracefully — a page with no `nav`/`footer` elements and no `[class*="nav"]`-
style chrome simply matches nothing in the hide list, and prints as-is.

Optional escape-hatch classes a paper's own template may use:

| Class | Applies to | Effect |
|---|---|---|
| `.no-print` / `.working-papers-print-hide` | any element | Hidden in print (use for on-screen-only affordances the broad chrome selectors don't catch) |
| `.working-papers-print-keep` | any block-level element | Never splits across a page break |
| `.page-break-before` / `.page-break-after` | any element | Forces a page break immediately before/after — use sparingly, e.g. to start the rigor block or an appendix on its own page |

## ARIA / accessibility notes

- Print CSS only affects the print medium; no on-screen accessibility
  semantics change. `print-color-adjust: exact` preserves color-coded
  meaning (e.g. a callout fill) into print rather than letting the browser
  strip it, which would otherwise leave only color as the (now-lost) signal.
- The `a[href^="http"]::after { content: "" }` rule is a deliberate no-op
  today (JOURNAL body text carries zero outbound links by editorial rule) —
  left in place as the hook if a future paper format ever needs a printed
  URL after a link.

## Open question

The chrome-hiding selector list is deliberately broad rather than enumerated
per site. If a 7th JOURNAL-publishing site adopts this file and its own
chrome doesn't match any of `[class*="nav"|"breadcrumb"|"footer"|"theme-
toggle"|"lang-switch"|"search"]`, widen this list rather than adding a
per-site override block — keeping this a single, unforked file is the point.
