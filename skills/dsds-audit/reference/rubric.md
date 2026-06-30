# The audit rubric

How to score a zeroheight page for DSDS 0.12.0 machine-readability and turn the
result into a prioritised list of changes **a customer can actually make in
zeroheight**. The goal is not a grade — it's to tell an author the exact authoring
moves that make their docs parseable by tools and agents, in the order that
matters.

This rubric scores against the **zeroheight authoring moves** in
`reference/zeroheight-authoring-pattern.md`, not against raw DSDS JSON fields. A
finding the customer can't act on in the editor is not a customer finding.

## Rating scale (per check)

- **Pass** — the authoring move is in place; the MCP reliably extracts it.
- **Partial** — the information exists but not via the move; a human can read it, a
  parser/agent can't reliably key off it. *Most existing-docs findings land here.*
- **Missing** — content and structure both absent.
- **N/A** — doesn't apply to this entity kind.

A **Partial** is good news framed as a small change ("you already have this — here's
the move that makes it machine-readable"), not a failure. Phrase it that way; it
changes how the report lands.

## Who-can-act tag (required on every finding)

Tag each finding so the customer never gets advice they can't use:

- **[Author]** — doable in the zeroheight editor now. **These are the report.**
- **[Platform]** — only zeroheight can do it (JSON export, server-derived ids).
  **Omit entirely.** Do not list these anywhere in the report — no per-page row, no
  appendix, no "for zeroheight" section. The tag exists only so you can filter them
  out; the customer should never see them.
- **[New content]** — net-new material someone must write (acceptance criteria,
  agent rules). Author-doable, so it belongs in the report; flag that it's authoring
  effort, not a tweak, and draft the content for them (see below).

## Severity (drives ordering)

- **P1 / blocker** — without it the entity can't be parsed or placed: ambiguous
  entity kind, duplicate/colliding titles, empty/stub page, token shown as raw hex
  with no token-name reference.
- **P2 / core** — the moves that make docs useful to tools: Level column on
  guidelines, consistent props/variants tables, canonical names for states/anatomy,
  status vocabulary, linked alternatives, `introduction` filled.
- **P3 / enrichment** — the agent-grade layer: the **For Agents tab** (hard rules,
  disambiguation, acceptance criteria), tags/category, per-platform status detail.

Order every page's findings P1 → P2 → P3 so a customer can work top-down and stop
when their budget runs out, having done the highest-value work.

## Per-entity check sets

Run only the checks for the page's `kind`. For each, record rating + who-can-act +
severity + the specific authoring move to make.

### Envelope (all entities)
- Title unique, clean, consistent (de-facto identifier) — [Author] P1
- `introduction` filled (→ description) — [Author] P2
- Status set, from the controlled vocabulary — [Author] P2
- Tags / category convention (nav grouping helps) — [Author] P3
- Internal links to related pages (Figma/Storybook/related entities) — [Author] P3

### component
- Guidelines table with **Level column** (controlled vocab) — [Author] P2
- "When to use / not to use" or Do/Don't, with **linked alternatives** — [Author] P2
- **Props table** with fixed column schema — [Author] P2
- **Variants table** (Type column = flag vs enum) — [Author] P2
- States named consistently, one per subsection/row — [Author] P2
- Anatomy parts named/numbered — [Author] P2
- Accessibility section present — [Author] P2
- Token references by name (not raw hex) — [Author] P2
- **For Agents tab** (hard rules + disambiguation) — [Author/New content] P3
- **Acceptance criteria** (in For Agents tab) — [New content] P3
- Content/UX-writing do/don't itemised — [Author] P3

### foundation
- Principles as titled items — [Author] P2
- Guidelines table with Level column — [Author] P2
- Scale / motion expressed as tables where relevant — [Author] P2
- Token references by name — [Author] P2
- For Agents tab (hard rules, e.g. "reference semantic, never primitive") —
  [Author/New content] P3

### pattern
- Page not empty/stub (content not stranded in a sibling subpage) — [Author] P1
- Member components named and **linked**, with role/required noted — [Author] P2
- Interaction flow as ordered steps — [Author] P2
- Guidelines table with Level column; when-to-use — [Author] P2
- For Agents tab — [Author/New content] P3

### token / token-group
- Token names follow the system convention (tier-type-context-name) — [Author] P1
- Values shown as **token-name references / synced specimens**, not raw hex —
  [Author] P1
- Grouping/tiers clear (primitive/brand/semantic) — [Author] P2
- Usage guidance (reference semantic, not primitive) — [Author] P3

### theme
- Per-theme **override table** (Token / value / Description) — [Author] P1
- Overrides reference token names, not raw values — [Author] P2
- "When to use this theme" + best-practices table with Level — [Author] P2/P3

### guide
- Classified as guide (not mis-audited as a component) — P1
- Procedural content as **ordered steps** — [Author] P2
- Clear headings / sections — [Author] P3

### For Agents tab — dedicated checks (components & patterns)
- Tab present — P3
- Hard rules as MUST / MUST-NOT (controlled vocab) — P3
- Disambiguation naming/linking look-alikes — P3
- Acceptance criteria as testable statements with id + check + mode — P3
- Placed consistently (same name/position across components) — P3

## Scoring

Per page:

```
applicable = Pass + Partial + Missing            (exclude N/A)
score = (Pass + 0.5 * Partial) / applicable       → 0–100%
```

Report the score **with** the P1/P2/P3 counts and the who-can-act split — the score
alone hides that one P1 blocker outweighs five P3 gaps. Roll page scores into a
styleguide average, but always lead with blocker counts.

> Calibrate, don't inflate. A page of beautiful prose with no consistent
> tables/levels/links is ~0–20%, not 70%. An honest gap picture is the whole value;
> a generous score that says "mostly there" when no parser can read the docs is
> worse than useless.

## Turning checks into changes

Every finding must be a concrete authoring move, located on the page, referencing
the page's own content, with the DSDS intent named so the author understands why.
Not "add a guidelines block" but:

> **Button › Guidelines ([Author], P2, Partial).** The "button vs link" and "one
> primary per surface" rules are prose. Put them in a guidelines **table with a
> Level column** — "Use a link for navigation" → `Must not`; "one primary per
> surface" → `Must` — reusing the Required/Encouraged pattern already on your Color
> page. *DSDS intent: RFC 2119 levels so a parser separates hard rules from soft.*

## Draft ready-to-paste snippets (the most valuable output)

The findings table tells the author *what's wrong*; the **ready-to-paste section
gives them the fix already written**, so they can update zeroheight piece by piece
(until the MCP supports writes). For every [Author] or [New content] finding that
can be expressed as content, draft the actual segment — not the whole page, just the
specific block to paste — built from the page's own material.

Rules for snippets:
- **Real content, not placeholders.** Pull the page's actual rules, states, props,
  token names into the snippet. A guidelines snippet for Button must contain
  Button's real rules with levels assigned.
- **One segment per snippet**, each labelled with exactly where it goes
  ("Paste into: Button › a new *For Agents* tab").
- **In a fenced code block** so it copies cleanly.
- **Use only authorable constructs** — Markdown tables, headings, callouts, links.
  Never emit JSON for the customer to paste; they can't author it.
- **Always draft a For Agents tab** when the page lacks one — this is the single
  most useful snippet. Fill it with the page's real hard rules, disambiguation, and
  acceptance criteria.

See `reference/zeroheight-authoring-pattern.md` for the move per construct and the
For Agents tab structure, and `templates/audit-report.md` for how findings and
snippets roll up.
