# Per-kind sections + machine-readable shape

The normative entry-kind → section/field model from DSDS 0.20.1. Unlike 0.12.0
(where each entity accepted a fixed set of scoped *blocks*), **0.20.x lets any entry
kind use any section kind** — `guidelines`, `definitions`, `steps`, `section` — each
flagged `for: all | human | agent`. What differs per kind is the *scoped fields* an
entry carries beyond its sections (component `traits`, token `source`, …) and which
sections are *expected* in practice. Use this to decide, for a given page, which
sections/fields are *expected*, which are *missing*, and whether a *present* one
carries the structured content that makes it machine-readable.

## Section kinds (available to every entry kind)

| section `kind` | what it holds | typical `context` |
| --- | --- | --- |
| `guidelines` | usage rules, each with an RFC 2119 `level` + rationale | — |
| `definitions` | term/definition items: anatomy parts, props/API, glossary, keyboard, events | `anatomy`, `terms`, `keyboard`, `events` |
| `steps` | ordered/unordered procedures or checklists | — |
| `section` | generic prose (`freeform`) or loosely structured `items` | — |

Every section also carries `for` (audience). A section with `for: agent` is the
agent-only layer (0.12.0's `agentDocumentBlocks`).

> A section kind is never "wrong for this entity" — the pairing is unrestricted.
> The findings are instead: an *expected* section is **missing**, or a *present*
> section is unstructured (**Partial**). There is no "block can't live on this
> entity" schema error to flag anymore; drop that 0.12.0 check.

## Scoped fields per entry kind (beyond sections)

| entry kind | scoped fields it carries |
| --- | --- |
| `component` | `traits` (boolean/enum), `combos`, `sourceFiles`, `imports`, `specs` |
| `token` | `tokenType`, **required** `source` (DTCG ref), `combos` |
| `theme` | `colorScheme` (light/dark), **required** `source` (DTCG ref) |
| `system` | envelope + sections only (overview, principles, getting-started) |
| `entry` (generic) | envelope + sections only — **foundations, patterns, guides, glossaries** all live here; the sections carry the meaning |

## What each thing needs to count as machine-readable

More than a heading. For each, the audit checks whether content is expressed in the
structured shape, or merely as prose/tables a human can read but a parser cannot key
off. "Partial" = present but unstructured; "Pass" = structured to the shape below.

### Sections (any entry)

- **`guidelines`** — `items[]`, each a guideline with an RFC 2119 `level`
  (`must` / `should` / `may` / `should-not` / `must-not`) + a `statement` + rationale.
  Recommend/discourage guidance uses `framing: when-to-use` and an `alternatives`
  ref. *Bulleted dos/don'ts are Partial until each item gets a `level`.*
- **`guidelines` → testable** — a guideline becomes enforceable when its `statement`
  is testable and it carries `checkedBy` (`automated` / `assisted` / `manual`) plus,
  where automatable, a `checks` ref (`rel: test` / `lint-rule`, e.g. an axe-core
  rule). This replaces 0.12.0's separate `criteria`/`verification`. Accessibility and
  usage rules are strongest here; narrative-only a11y is Partial.
- **`definitions`** — `items[]`, each a term with a stable `id` + definition. With
  `context: anatomy`, the items are the component's **named parts** (0.12.0's
  `anatomy` block). With `context: terms`, a glossary. A prop/API table is a
  `definitions` section whose items are `{ id, type, default?, required?, description? }`
  — a rendered props table alone is Partial until it's structured (or its
  `source`/Storybook ref is declared as the machine source via `specs`).
- **`steps`** — ordered, structured procedure/checklist items (0.12.0's guide
  `steps`). A prose walkthrough is Partial.
- **`section` (generic)** — `items` of `{ title, body }` or `freeform` rich text;
  the catch-all for prose that doesn't fit a typed kind.
- **`for: agent`** — the agent-only audience on any of the above; its absence is a
  high-value finding for AI-targeting systems (see rubric P3).

### Component scoped fields

- **`traits`** — replace 0.12.0's `variants` + `states`. Each is `boolean` (a flag)
  or `enum` (named `values[]`, first is default), each with a stable `id`; `setBy`
  distinguishes consumer-set (a prop/variant) from component-set (a state like
  hover/focus/disabled). A prose "by hierarchy / by shape" section, or screenshots
  captioned "Hover / Focus", is Partial until expressed as keyed traits.
- **`combos`** — pairing rules `{ subject, items[], level, note? }` between traits
  (or a trait and a token), the `level` saying how strict. (0.12.0 had no direct
  equivalent; treat as enrichment.)
- **`sourceFiles` / `imports` / `specs`** — declared source, import snippet, and a
  ref to a machine-readable API contract (CEM / DS Contracts). Component composition
  and cross-references live in `refs`, not in a block.

### Token / theme scoped fields

- A **token** needs `id`, `tokenType`, and a **required `source`** ref to the DTCG
  file. No `source` = not machine-traceable to a value. Groupings use
  `metadata.group` (no `token-group` kind, no `children[]`).
- A **theme** needs `colorScheme` and a **required `source`** ref to the DTCG file
  holding the override values. The overridden token names live in DTCG, not inline
  (no `overrides[]` array).
