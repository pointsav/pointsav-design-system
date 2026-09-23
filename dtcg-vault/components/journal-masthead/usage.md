<div class="doc-header">
<span class="eyebrow">Components</span>
<div class="doc-header__badges">
<span class="badge">1 variant</span>
<span class="badge badge--brand">Tokens-backed</span>
<span class="badge">WCAG 2.2 AA target</span>
</div>
<p class="doc-header__lead">Top-of-paper surface for a /working-papers JOURNAL
article: an engine-generated notice blockquote plus a one-line meta-line stating
imprint, version, and license.</p>
<div class="registry-note"><span>Rendered from</span> <code>components/journal-masthead/recipe.json</code></div>
</div>

## When to use Journal Masthead Notice

Use on every `/working-papers` JOURNAL article, on all 6 product sites. It is
**not** the site's global footer trademark block and does not replace it; it is
**not** a hand-authored blockquote — the article's own markdown source never
contains masthead text, only the frontmatter fields (`site`, `imprint`, `version`,
`license`) the render engine composes it from.

The notice text itself is resolved **per brand**, not per site individually and not
shared across all 6:

- **PointSav-brand sites** (`home.pointsav.com`, `software.pointsav.com`,
  `design.pointsav.com`) render `legal-tokens-pointsav.yaml`'s
  `disclaimers.no_investment_advice` text — PointSav-brand properties structurally
  carry no investment/forward-looking-statement disclaimer.
- **Woodfine-brand sites** (`home.woodfinegroup.com`, `bim.woodfinegroup.com`,
  `gis.woodfinegroup.com`) render `legal-tokens-woodfine.yaml`'s
  `disclaimers.forward_looking.short` text — the real securities-law
  forward-looking-information notice.

Full mapping and rationale: `tokens/linguistic/register-journal.yaml`'s
`forward_looking_notice_by_brand` block.

One instance per article, always at the very top of the rendered page, before the
meta-line and before the lead paragraph.

---

## Markup

Never authored directly — the render engine composes both lines from frontmatter.
Illustrative output only:

```html
<blockquote class="journal-masthead__notice">
  PointSav Digital Systems software and documentation are provided for
  operational, research, and development purposes. Nothing on PointSav
  properties constitutes investment advice or a solicitation to invest in any
  Woodfine Capital Projects partnership or direct-hold solution.
</blockquote>
<p class="journal-masthead__meta">Working Paper PDS-008 · v1.0.0 · CC-BY-4.0</p>
```

## Accessibility

`blockquote` is native semantic HTML — no additional ARIA role needed. The
meta-line is a plain `<p>`, not a heading, so heading-based in-page navigation
(e.g. `wiki-toc-sidebar`) does not pick it up as a section.
