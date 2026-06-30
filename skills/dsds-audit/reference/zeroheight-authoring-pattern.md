# The zeroheight authoring pattern (DSDS, the authorable way)

This is the heart of the customer-facing audit. zeroheight authors cannot emit
DSDS JSON — they write pages with titles, headings, tables, callouts, tabs, links,
and token specimens. But **DSDS's value isn't the JSON syntax; it's the
conventions the syntax enforces** — stable names, controlled vocabularies,
consistent table schemas, explicit relationships, testable statements. Every one
of those is authorable in zeroheight.

So the audit never tells a customer to "add an `identifier` field." It tells them
the **zeroheight move** that produces the same machine-readable effect, and
explains the DSDS intent behind it. Doing the move pays off twice: it improves what
the MCP infers today, and it pre-shapes the content to convert cleanly if/when
zeroheight ships DSDS export.

## How to read this file

Each construct below gives: the DSDS intent, the **zeroheight move** a customer can
actually make, what the MCP sees in `get-page`, and the fidelity ceiling (how close
the approximation gets). Score findings against the *move*, not the JSON — see
`rubric.md`.

## Confirmed affordances (observed in the Baseline styleguide)

These are real and authorable; the audit can safely recommend them:

- Page **title** + stable URL **slug** (`/p/226512-button`).
- Page **`introduction`** frontmatter (→ description).
- **Headings** (page-level tabs serialize as `# H1`; subheads as `##`/`###`).
- **Tables** with author-defined columns (the Level and Status columns Baseline
  already uses are the key levers).
- **Callouts** (`<callout background>`), **Do/Don't** content, **tabs**.
- **Internal page links** (to alternative components, to the status registry).
- **Token-name references** / alias syntax (`{primitive.color.pink.300}`), not raw
  hex.
- A central **Component Status** matrix + per-page status with a controlled
  vocabulary (Live / In progress / Backlog / Ready / To do).
- Per-theme **override tables** (Token / value / Description).

> The audit should treat these as the available toolkit. If a finding can't be
> expressed with one of these moves, it's not a customer finding — it's platform or
> net-new content, and belongs in a separate "for zeroheight" appendix, not in the
> customer's action list.

## Translation table

### identifier (stable machine key)
- **DSDS intent:** a stable key tools/agents reference; distinct from display name.
- **zeroheight move:** keep page **titles unique, clean, consistently cased**; the
  slug becomes the de-facto identifier. For sub-parts (states, anatomy, variants),
  use the **same canonical label everywhere it appears**. Eliminate duplicate
  titles (e.g. "Checkbox" vs "Checkbox 1") — they collide.
- **MCP sees:** `title:` + `url:` slug in frontmatter; labels in headings/table
  first-columns.
- **Fidelity:** high for entities (slug is genuinely stable); medium for sub-parts
  (consistency-dependent, no enforced key).

### description
- **DSDS intent:** one-line entity summary.
- **zeroheight move:** fill the page **`introduction`** field.
- **MCP sees:** `introduction:` in frontmatter.
- **Fidelity:** high — near-exact match.

### status (overall + per-platform)
- **DSDS intent:** lifecycle state, optionally per platform.
- **zeroheight move:** use the **Component Status matrix** + per-page status with a
  **fixed vocabulary**. Map the columns (Documentation / Design / Storybook) to
  per-platform status. Pin the vocabulary (see below).
- **MCP sees:** the status matrix table; per-page status strings.
- **Fidelity:** high — Baseline already does this; just standardize the words.

### guidelines with RFC 2119 levels
- **DSDS intent:** each rule tagged must / should / should-not / must-not so a
  parser separates hard from soft.
- **zeroheight move:** a **guidelines table with a Level column** drawn from a
  controlled vocabulary (Baseline's "Required/Encouraged" pattern), *or* start each
  rule with a fixed verb ("Must…/Should…/Never…/Avoid…"). Add a Why/rationale
  column.
- **MCP sees:** a table with a Level column, or consistent leading verbs.
- **Fidelity:** high — this is the cleanest approximation in the whole pattern, and
  it already exists on Color and Theming.

### useCases stance + alternative
- **DSDS intent:** recommended vs discouraged, with a linked alternative.
- **zeroheight move:** explicit **"When to use" / "When not to use"** sections or
  **Do/Don't** blocks; for the alternative, **link the alternative component's
  page** ("use [Link] instead").
- **MCP sees:** stance from section structure; the internal link as the
  cross-reference.
- **Fidelity:** medium-high — stance is clear; the link approximates
  `alternative.identifier`.

### api (typed properties)
- **DSDS intent:** properties with identifier / type / default / required.
- **zeroheight move:** a **props table with a fixed column schema** (Prop / Type /
  Default / Required / Description), identical across every component, ideally
  Storybook/Code-Connect-synced.
- **MCP sees:** a consistent Markdown table.
- **Fidelity:** high if the schema is consistent — Button already demonstrates it.

### variants (flag vs enum)
- **DSDS intent:** boolean flag vs named-option enum, each keyed.
- **zeroheight move:** a **component-properties table** with Property / Type
  (Variant=enum, Boolean=flag) / Options / Default — Button already has this.
- **MCP sees:** the table; Type column distinguishes flag from enum.
- **Fidelity:** high.

### states / anatomy
- **DSDS intent:** named parts/states each with an identifier.
- **zeroheight move:** one **subsection or table row per state/part with a
  consistent canonical name**; number anatomy parts.
- **MCP sees:** the names in headings/rows.
- **Fidelity:** medium-high — names carry meaning; no enforced key.

### token source (DTCG)
- **DSDS intent:** a token points to its value in the DTCG file.
- **zeroheight move:** always reference the **canonical token name** in
  specimens/tables (`brand.color.brand.100`), via the **synced token integration**,
  never hand-typed hex. The token name is the pointer.
- **MCP sees:** token names / alias syntax in tables.
- **Fidelity:** high for traceability (name → source); values still live in DTCG by
  design.

### theme overrides
- **DSDS intent:** list which token identifiers change per theme.
- **zeroheight move:** the **per-theme override table** (Token / value / Description)
  — Baseline's Theming page already does this.
- **MCP sees:** the override table.
- **Fidelity:** high.

### criteria with verification
- **DSDS intent:** testable statements, some automated with a named check.
- **zeroheight move:** an **"Acceptance criteria" checklist/table** of testable
  statements; where automatable, **name the tool/rule in text** ("axe-core:
  button-name"). Best placed in the For Agents tab (below).
- **MCP sees:** a checklist of statements + tool names.
- **Fidelity:** medium — readable and self-checkable by an agent; not runnable
  without a parser.

### agentDocumentBlocks (the agent layer)
- **DSDS intent:** firm, agent-only rules + look-alike disambiguation.
- **zeroheight move:** a **"For Agents" tab** (full spec below).
- **MCP sees:** a `# For Agents` H1 section in `get-page`.
- **Fidelity:** medium now (visible heading, inferred authority), high if zeroheight
  gives the tab first-class MCP treatment.

## The "For Agents" tab — spec

A dedicated page tab on components (and patterns) holding the firm, machine-facing
content that would clutter the human narrative. It's the single highest-leverage
move in this pattern because it turns the two weakest, most-inferred parts of DSDS
(the agent layer and verification) into clearly-labeled, reliably-retrieved
content — and because **zeroheight owns the MCP**, it's the natural hook for
first-class treatment later (elevate the section in `get-page`; map it to
`agentDocumentBlocks` + `criteria` in a future export).

### Why it works through the MCP
`get-page` returns page-level tabs as `# H1` sections, so the tab's full content
reaches any agent that calls `get-page`. Retrieval is reliable. Caveats to design
around: it won't appear in `search-pages` snippets (consumers must call
`get-page`); on very long pages, keep it tight and prominent so attention doesn't
fade; it's human-visible, so resist softening the rules for human readers.

### Recommended structure
Keep it terse and imperative — this is for action, not narrative.

```
# For Agents

## Hard rules
- MUST NOT use this component to navigate to a URL — use Link.
- MUST provide an accessible name (visible text or aria-label).
- MUST limit one primary-emphasis button per surface.

## Disambiguation (when NOT this component)
- vs Link — Link navigates; Button performs an in-page action.
- vs Icon Button — use Icon Button for icon-only affordances.

## Acceptance criteria
- [ ] icon-only-has-name — every icon-only instance exposes a non-empty
      accessible name. Check: axe-core / button-name. (automated)
- [ ] focus-visible — focus state is visible and ≥ hover prominence. (manual)
- [ ] contrast-aa — label/background ≥ 4.5:1. Check: axe-core / color-contrast.
      (automated)
```

### Authoring conventions for the tab
- Lead with **Hard rules** as MUST / MUST NOT bullets (controlled vocabulary).
- **Disambiguation** entries always name and ideally link the look-alike component.
- **Acceptance criteria** are testable statements; give each a stable slug-style id
  and, where automatable, a `Check: tool / rule` and a mode (automated / assisted /
  manual). This is exactly the shape a future export maps to DSDS `criteria`.
- Place the tab consistently (same name, same position) on every component so the
  MCP — and customers — always find it in the same place.

## Controlled vocabularies (pin these per system)

- **Guideline levels:** `Must` / `Should` / `Should not` / `Must not`
  (or Baseline's Required/Recommended/Discouraged — pick one and use it everywhere).
- **Status:** `Live` / `In progress` / `Backlog` / `Deprecated` for docs;
  `Ready` / `In progress` / `To do` for platform readiness. Keep them stable so the
  MCP reads a closed set, not free text.

## Delivering snippets: paste fidelity

zeroheight's editor ignores raw Markdown on paste — it reads only the `text/plain`
clipboard flavor, so pasted `|`/`#` arrive as literal characters. The fix is to have
customers copy **rendered** content (which carries `text/html`), which the editor
maps to its own blocks. That's what `templates/paste-pack.html` does: each snippet is
rendered, and the Copy button selects the rendered node so the clipboard gets real
HTML. Always ship the paste pack alongside the report; never expect customers to
paste raw Markdown or JSON.

What survives a paste vs what needs a container first:
- **Pastes cleanly into existing content:** tables, H1–H3 headings, bullet/numbered
  lists, bold/italic, links, paragraphs.
- **Needs a zeroheight UI container created first, then paste the body in:** a new
  **tab** (e.g. For Agents), **callouts**, **Do/Don't** blocks, **token specimens**.
  Say so in the snippet's note so the author adds the container before pasting.

## What is NOT a customer finding

Some things only zeroheight can do: emitting DSDS JSON, server-deriving identifiers
from slugs, structured `get-page` export, giving the For Agents tab first-class MCP
treatment. **Omit these from the report entirely** — no action row, no appendix, no
"for zeroheight" section. The audit is a customer's to-do list; platform work has no
place in it. Tag such findings [Platform] only so you can filter them out.
