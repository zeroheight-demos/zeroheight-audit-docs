---
mode: agent
description: Audit a zeroheight design system's docs for machine-readability against DSDS 0.12.0 and produce a prioritised, page-by-page list of authoring moves plus ready-to-paste segments.
---

# DSDS machine-readability audit (zeroheight)

Audit the user's **zeroheight** design system documentation against the **Design
System Documentation Spec** (DSDS, [designsystemdocspec.org](https://designsystemdocspec.org)),
pinned to **0.12.0**, and produce a concrete, page-by-page list of authoring moves
plus ready-to-paste segments.

Scope for this run (optional — if the user gave one, e.g. "just Components", honour it;
otherwise confirm scope before auditing everything): `${input:scope}`

## Prerequisites

1. The **zeroheight MCP** must be connected — tools `list-pages`, `search-pages`,
   `get-page`, `list-releases`, `get-page-asset`. It reads the user's live styleguide.
   If it is **not** connected, **stop** and tell the user to configure their personal
   zeroheight remote MCP URL (see [`COPILOT.md`](../../COPILOT.md)). Do **not** substitute
   a generic checklist — the audit is worthless without the live docs.

2. Read **all six** source files below before auditing. They are the source of truth;
   do not audit from memory of the spec, do not invent block kinds or levels, and do not
   recommend a change a customer can't make in the zeroheight editor.

   - [`skills/dsds-audit/reference/dsds-0.12.0-model.md`](../../skills/dsds-audit/reference/dsds-0.12.0-model.md) — the entity/block/criteria model, pinned.
   - [`skills/dsds-audit/reference/entity-block-rules.md`](../../skills/dsds-audit/reference/entity-block-rules.md) — which blocks each entity accepts and the structured shape each needs.
   - [`skills/dsds-audit/reference/zeroheight-mapping.md`](../../skills/dsds-audit/reference/zeroheight-mapping.md) — how to classify a zeroheight page to a DSDS entity and map its sections onto blocks.
   - [`skills/dsds-audit/reference/zeroheight-authoring-pattern.md`](../../skills/dsds-audit/reference/zeroheight-authoring-pattern.md) — **the heart of the audit**: each DSDS construct translated to the next-best zeroheight authoring move, controlled vocabularies, and the "For Agents" tab spec.
   - [`skills/dsds-audit/reference/rubric.md`](../../skills/dsds-audit/reference/rubric.md) — Pass/Partial/Missing rating, who-can-act tagging, P1/P2/P3 severity, scoring, phrasing.
   - [`skills/dsds-audit/templates/audit-report.md`](../../skills/dsds-audit/templates/audit-report.md) — the deliverable structure.
   - [`skills/dsds-audit/templates/paste-pack.html`](../../skills/dsds-audit/templates/paste-pack.html) — the paste-pack template (used in step 6 / output).

## Core principle

zeroheight authors cannot emit DSDS JSON — they write pages. But DSDS's value isn't the
JSON syntax, it's the conventions it enforces (stable names, controlled vocabularies,
consistent tables, explicit relationships, testable statements), and every one of those
is authorable in zeroheight. So this audit never tells a customer to "add an `identifier`
field." It recommends the **zeroheight authoring move** that produces the same
machine-readable effect, names the DSDS intent behind it, and tags who can act
(`[Author]` / `[Platform]` / `[New content]`). Findings only zeroheight can fix stay out
of the customer action list.

## Procedure

1. **Scope.** Call `list-pages` for the styleguide name and full nav tree. Confirm with
   the user: whole styleguide, or a subset? For a specific release, `list-releases` first,
   then pass that `releaseId` consistently to `list-pages` and `get-page`.
2. **Read each page.** For each in-scope page call `get-page` and read the Markdown. Don't
   infer content from the nav title — open the page. Image annotations are coordinate
   metadata only; note the limit rather than guessing.
3. **Classify** each page to a DSDS `kind` (component / token / token-group / theme /
   foundation / pattern / guide) using `zeroheight-mapping.md`. Flag ambiguous pages.
   Intros, changelogs, "welcome" pages are `guide`s.
4. **Rate** against the entity's check set (`rubric.md`), scoring against the authoring
   moves (`zeroheight-authoring-pattern.md`): **Pass / Partial / Missing / N/A**. Tag every
   finding `[Author]` / `[Platform]` / `[New content]`. Drop `[Platform]` items from the
   action list. Most real findings are Partial + `[Author]` — phrase as "you're close,
   here's the move," not failure. Hunt the recurring high-value gaps: colliding titles,
   empty pages, raw hex instead of token names, no Level column on guidance, inconsistent
   props/variants tables, unnamed states/anatomy, no linked alternatives, no For Agents tab.
5. **Make every finding a concrete authoring move** on the named page, referencing the
   page's own content, naming the DSDS intent. Never "add a guidelines block." Cite the
   customer's own pages where a good pattern already exists so findings read as
   "standardise what you already do." Order each page's findings P1 → P2 → P3.
6. **Draft ready-to-paste snippets** — real content, never placeholders, never JSON — in
   two forms: fenced code blocks in the Markdown report, and a companion `paste-pack.html`
   (built from the template) with one `.card` per snippet containing rendered semantic HTML
   so tables/headings/lists survive the clipboard. Always draft a **For Agents** tab when a
   page lacks one. State the container limit in each card's note (pasting fills content;
   new tabs, callouts, Do/Don't blocks, token specimens must be created in the editor UI
   first).
7. **Score and assemble** the report (`templates/audit-report.md`): lead with blocker
   counts and the author-actionable share, not the average. Surface cross-cutting
   system-level moves first. Include the For Agents tab coverage appendix. **Omit
   `[Platform]` findings entirely** from both deliverables.

## Calibration

- **Be honest, not generous.** Lovely prose with no structure is ~0–20%, not 70%.
- **DSDS references token values, it doesn't store them** — never flag "missing hex values."
- **Respect the entity/block rules** — a block on an entity that can't accept it is a
  schema error (P1), distinct from a missing block.
- **Stay located and specific** — tie every change to a page and its content.

## Output

Two companion files: the **Markdown audit report** and the **`paste-pack.html`**. Offer a
.docx/.pdf of the report if the user wants something shareable. Never put `[Platform]`
items in either file.
