# Per-entity document blocks + machine-readable shape

The normative block-to-entity mapping from DSDS 0.12.0
(`document-blocks-document-blocks`). Each entity type accepts its own scoped
blocks **plus** the general blocks. Use this table to decide, for a given page,
which blocks are *expected*, which are *missing*, and whether a *present* block
carries the structured fields that make it machine-readable.

General blocks (accepted by every entity): `guidelines`, `useCases`,
`accessibility`, `content`, `sections`, `checklist`.

| entity | scoped blocks it accepts (beyond general) |
| --- | --- |
| `component` | `anatomy`, `api`, `variants`, `states`, `design-specifications`, `imports` |
| `foundation` | `principles`, `scale`, `motion` |
| `pattern` | `interactions`, `anatomy`, `variants`, `states` |
| `token` / `token-group` | (general blocks only) |
| `guide` | `steps`, `imports` |

> A block that is not in an entity's accepted set is a schema error, not just a
> style issue. Flag "this section can't live on this entity type" distinctly from
> "this block is missing."

## What each block needs to count as machine-readable

A block is more than a heading. For each, the audit checks whether the underlying
content is expressed in the block's structured fields, or merely as prose/tables a
human can read but a parser cannot key off. "Partial" = the content exists but is
unstructured; "Pass" = structured to the shape below.

### General

- **`useCases`** — `items[]`, each `{ description, stance: recommended|discouraged }`;
  discouraged items SHOULD carry an `alternative` `{ identifier, rationale }`.
  Optional `purpose`. *Prose "when to use" lists are Partial until they carry the
  stance enum and alternative identifiers.*
- **`guidelines`** — `items[]`, each `{ guidance, rationale, level: must|should|should-not|must-not, category? }`.
  *Bulleted dos/don'ts are Partial until each gets an RFC 2119 level.*
- **`accessibility`** — structured a11y guidance; strongest when tied to
  `criteria` with `verification` (e.g. an `axe-core` check), not just narrative.
- **`content`** — content/UX-writing guidance; do/don't items should be itemised.
- **`sections`** — `items[]` of `{ title, body }` rich-text; the catch-all for
  prose that doesn't fit a typed block.
- **`checklist`** — itemised, ideally referencing `criteria` identifiers.

### Component-scoped

- **`anatomy`** — named parts, each with a stable `identifier` (not just an
  annotated image). A labelled diagram alone is Partial.
- **`api`** — `properties[]`, each `{ identifier, type, default?, required?, description? }`.
  A rendered props table is Partial until it is the structured api block (or its
  `source`/Storybook link is declared as the machine source).
- **`variants`** — typed as `flag` (boolean) or `enum` (named options) with
  `identifier`s. A prose "by hierarchy / by shape" section is Partial.
- **`states`** — named states each with an `identifier` (default, hover, focus,
  active, disabled, …). Screenshots with captions are Partial.
- **`design-specifications`** — structured specs (often the purpose-keyed
  `tokens` map linking design tokens to roles).
- **`imports`** — declared composition/imports.

### Foundation-scoped

- **`principles`** — `items[]` of `{ title, description }`.
- **`scale`** — structured scale steps (spacing/type ramp), not a prose list.
- **`motion`** — structured motion/easing/duration definitions.

### Pattern-scoped

- **`interactions`** — flow steps for the pattern.
- plus `anatomy`, `variants`, `states` as above.
- Pattern composition lives in the entity's `links` array
  (`{ kind, identifier, role, required }`), not in a block.

### Guide-scoped

- **`steps`** — ordered, structured procedure steps.
- **`imports`** — referenced material.

## Token / theme specifics

- A **token** needs `identifier`, `tokenType`, and a `source`
  `{ file, path }` pointing at the DTCG file. No `source` = not machine-traceable
  to a value.
- A **token-group** needs `children[]` (tokens or nested groups) and may share
  `tokenType`/`source` defaults.
- A **theme** needs `overrides[]` of `{ token, description }` naming which token
  identifiers change. Values live in DTCG, not here.
