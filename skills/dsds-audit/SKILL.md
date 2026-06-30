---
name: dsds-audit
description: >-
  Audit a zeroheight design system's documentation for machine-readability
  against the Design System Documentation Spec (DSDS, designsystemdocspec.org).
  Use this whenever someone wants to check, score, or improve how parseable,
  structured, or "agent-ready" / "AI-ready" their zeroheight docs are — phrases
  like "audit our design system docs", "are our docs machine-readable", "check
  our styleguide against DSDS", "how AI-ready is our documentation", "what should
  we change to make our components parseable", or "run a DSDS conformance check".
  Reads the live styleguide through the zeroheight MCP, classifies each page to a
  DSDS entity, and produces a granular, prioritised list of the exact changes to
  make. Use it even if the user doesn't say "DSDS" by name but is asking about
  documentation structure, machine/agent readability, or design-system docs QA.
---

# DSDS machine-readability audit

This skill audits a design system documented in **zeroheight** against the
**Design System Documentation Spec (DSDS)** and returns a concrete, page-by-page
list of changes that would make the docs machine-readable — parseable by tools and
consumable by agents, not just readable by people.

It targets a **pinned DSDS version** (currently **0.12.0**) so results are
reproducible. The spec is a draft and changes; this skill audits against the
frozen model in `reference/`, not against the live site.

**The core principle:** zeroheight authors cannot emit DSDS JSON — they write
pages. But DSDS's value isn't the JSON syntax, it's the conventions it enforces
(stable names, controlled vocabularies, consistent tables, explicit relationships,
testable statements), and every one of those is authorable in zeroheight. So this
audit never tells a customer to "add an `identifier` field." It recommends the
**zeroheight authoring move** that produces the same machine-readable effect, names
the DSDS intent behind it, and tags who can act ([Author] / [Platform] / [New
content]). Findings only zeroheight can fix stay out of the customer action list.

## What you need

- The **zeroheight MCP** connected (tools `list-pages`, `search-pages`,
  `get-page`, `list-releases`, `get-page-asset`). It exposes the user's styleguide.
- The reference files in this skill:
  - `reference/dsds-0.12.0-model.md` — the entity/block/criteria model, pinned.
  - `reference/entity-block-rules.md` — which blocks each entity accepts and the
    structured shape each needs (the DSDS-intent layer behind the moves).
  - `reference/zeroheight-mapping.md` — how to classify a zeroheight page to a DSDS
    entity kind and map its sections onto blocks.
  - `reference/zeroheight-authoring-pattern.md` — **the heart of the audit**: each
    DSDS construct translated to the next-best zeroheight authoring move, the
    controlled vocabularies, and the "For Agents" tab spec. Recommendations come
    from here.
  - `reference/rubric.md` — the Pass/Partial/Missing rating, who-can-act tagging,
    P1/P2/P3 severity, scoring, and how to phrase findings.
  - `templates/audit-report.md` — the deliverable structure.

Read all six before auditing. They are the source of truth; do not audit from
memory of the spec, do not invent block kinds or levels, and do not recommend a
change a customer can't make in the zeroheight editor.

If the zeroheight MCP is not connected, stop and tell the user — the audit reads
their live docs and cannot run without it. Don't substitute a generic checklist.

## Procedure

### 1. Scope the audit
Call `list-pages` to get the styleguide name and full navigation tree. Confirm
with the user: audit the whole styleguide, or a subset (e.g. just Components)?
For large systems, agree a scope rather than silently auditing everything. If the
user wants a specific release, use `list-releases` first, then pass that
`releaseId` consistently to `list-pages` and `get-page` (page IDs differ per
release).

### 2. Read each page
For each in-scope page, call `get-page` and read the returned Markdown. Don't
infer content from the navigation title — open the page. Image annotations are
coordinate metadata only; you can list anatomy labels but not infer what they
point at without the image, so note that limit rather than guessing
(see `reference/zeroheight-mapping.md`).

### 3. Classify to a DSDS entity
Using `reference/zeroheight-mapping.md`, assign each page a `kind`
(component / token / token-group / theme / foundation / pattern / guide). Record
the classification and flag any page that's ambiguous or spans two kinds — let a
human correct it rather than burying the uncertainty. Intros, changelogs, and
"welcome" pages are `guide`s, not broken components.

### 4. Rate against the authoring moves
Map the page's sections to DSDS blocks (`zeroheight-mapping.md`), then run the
entity's check set from `reference/rubric.md`, scoring against the **zeroheight
authoring moves** in `reference/zeroheight-authoring-pattern.md`. For each check:

- **Pass** — the authoring move is in place; the MCP extracts it reliably.
- **Partial** — content present but not via the move; not parseable.
- **Missing** — absent.
- **N/A** — doesn't apply to this kind.

Tag every finding **[Author]** / **[Platform]** / **[New content]**. Drop
**[Platform]** items from the action list (appendix only). Most real findings are
**Partial, [Author]** — the writing is good, the convention is missing. Phrase that
as "you're close, here's the move," not failure. Look specifically for the recurring
high-value gaps: colliding/duplicate titles, empty pages, raw hex instead of token
names, no Level column on guidance, inconsistent props/variants tables, unnamed
states/anatomy, no linked alternatives, and no For Agents tab.

### 5. Make every finding a concrete authoring move
This is the whole value of the audit. Never write "add a guidelines block." Write
the specific zeroheight move, on the named page, referencing the page's own content,
and name the DSDS intent:

> **Button › Guidelines ([Author], P2, Partial).** "Use a link for navigation" and
> "one primary per surface" are prose. Put them in a guidelines **table with a Level
> column** — `Must not` / `Must` — reusing the Required/Encouraged pattern already
> on your Color page. *DSDS intent: RFC 2119 levels so parsers separate hard rules
> from soft.*

Cite their own pages where a good pattern already exists (Color/Theming use a Level
column; Button has a clean props table; the Component Status matrix has a status
vocabulary) so findings read as "standardise what you already do." Order each page's
findings P1 → P2 → P3 so a customer can work top-down and stop when budget runs out.

### 6. Draft ready-to-paste snippets (Markdown report + paste pack)
For each page, after the findings table, write a **Ready to paste** section: the
actual segments to drop into the editor, built from the page's own content, one per
actionable finding (`rubric.md` → "Draft ready-to-paste snippets"). Real content,
never placeholders, never JSON. **Always draft a For Agents tab** when the page lacks
one — fill it with the page's real hard rules, disambiguation, and acceptance
criteria. This is what lets a customer update piece by piece while the MCP is still
read-only.

Produce the snippets in **two forms**:
- In the Markdown report, as fenced code blocks (readable record).
- In a companion **`paste-pack.html`** built from `templates/paste-pack.html` —
  one `.card` per snippet, each `.snippet` containing rendered, semantic HTML
  (`<table>`, `<h2>`, `<ul>`, `<a>`). This is the file customers actually copy from:
  zeroheight ignores raw Markdown on paste (it reads only `text/plain`), but the
  paste pack's Copy button puts real `text/html` on the clipboard so tables,
  headings and lists keep their formatting.

Remember the container limit, and state it in each card's note: pasting fills
*content*, but zeroheight containers — a new **tab** (e.g. For Agents), callouts,
Do/Don't blocks, token specimens — must be created in the editor UI first, then the
body pasted in. Tables, headings, lists, bold, and links paste cleanly.

### 7. Score and assemble the report
Compute the per-page score (`rubric.md`), roll up to a styleguide average, but
**lead with blocker counts and the author-actionable share, not the average** — one
P1 outweighs five P3s. Surface the cross-cutting **system-level moves** first:
conventions that, fixed once, collapse many per-page findings (standardise a Level
column; pin the status vocabulary; add a For Agents tab everywhere). Include the
**For Agents tab coverage** appendix since it's usually the biggest gap. **Omit
[Platform] findings entirely — no "for zeroheight" section anywhere.** The report is
the customer's to-do list; if they can't do it in the editor, it doesn't appear.
Fill in `templates/audit-report.md`, save it as Markdown, and offer a .docx/.pdf
version if they want something shareable.

## Calibration

- **Be honest, not generous.** A page of lovely prose with no structure is ~0–20%,
  not 70%. The audit's worth is an accurate gap picture; an inflated score that
  says "mostly there" when no parser can read the docs is worse than nothing.
- **DSDS references token values, it doesn't store them.** Never flag "missing hex
  values" — values live in the W3C DTCG file a token `source`-s to.
- **Respect the entity/block rules.** A block on an entity that can't accept it is
  a schema error (P1), distinct from a missing block.
- **Stay located and specific.** Generic advice ("add identifiers everywhere") is
  the failure mode to avoid; tie every change to a page and its content.

## Output

Two companion files:

1. A **Markdown audit report** following `templates/audit-report.md`: executive
   summary with the headline numbers, system-level moves, a budget-ordered sequence,
   per-page findings tables (`check · rating · who · severity · the move`), each
   page's Ready-to-paste snippets, and the For Agents coverage appendix. Granular
   and actionable — the customer acts on it page by page.
2. A **`paste-pack.html`** from `templates/paste-pack.html` — the same snippets
   rendered with Copy buttons, so customers paste into zeroheight with formatting
   intact while the MCP is read-only.

Offer a .docx/.pdf of the report if they want something shareable. Never put
[Platform] items in either file.
