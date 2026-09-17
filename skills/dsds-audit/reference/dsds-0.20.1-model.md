# DSDS 0.20.1 — frozen reference model

This is a pinned, condensed snapshot of the Design System Documentation Spec
(DSDS) **0.20.1 draft**, captured for reproducible audits. The canonical source
is https://designsystemdocspec.org/ and the JSON Schema at
https://github.com/somerandomdude/design-system-documentation-schema
(`schema/dsds.bundled.schema.json`, published per version at
https://designsystemdocspec.org/v0.20.1/dsds.bundled.schema.json). DSDS is a draft
and will change — when you bump the version this skill targets, re-snapshot this
file and update the rubric, then re-tag the plugin.

> Audit against the version recorded here, not against whatever is live. A moving
> target makes audits non-reproducible, and customers need stable results they
> can act on over several sprints.

## What this model is for (and what the audit never asks a customer to do)

This file describes the **conventions** a machine-readable design system follows —
stable ids, RFC 2119 levels, typed sections, explicit relationships, testable
checks, an agent-facing layer. Those conventions are the audit's target.

DSDS 0.20.x authors documents as **YAML** (or JSON) files. **The audit never asks a
zeroheight customer to write YAML or JSON.** zeroheight authors write pages, not
DSDS documents. So this model is the thing we *approximate*: for every convention
below, the audit recommends the **zeroheight authoring move** that produces the same
machine-readable effect (see `zeroheight-authoring-pattern.md`), and never the DSDS
file itself. Read this model to understand *why* a move matters, not to hand a
customer a schema to fill in.

## What DSDS is (and isn't)

DSDS is a machine-readable format that documents the *how and why* of a design
system — the system itself, its components, tokens, themes, and anything else. It
does **not** carry token *values*; that is the job of the W3C Design Tokens (DTCG)
format, which DSDS links to via a token or theme's `source`. It also does not
duplicate API contracts (it points at a Custom Elements Manifest or similar via
`specs`) or stories. An audit that flags "missing hex values" is misreading the
spec — DSDS references values, it does not store them.

The whole point is one source of truth that serves three readers: humans, parsers,
and agents. Machine-readability means a parser or agent can reliably extract
structure (kinds, levels, ids, relationships, testable checks) without scraping
prose.

## Document shape

A DSDS document requires three things at the root:

- `schemaVersion` — the spec version (e.g. `"0.20.1"`).
- `name` — the design system's display name.
- `entries` — an array of entries (the system, components, tokens, themes, …).

```yaml
$schema: https://designsystemdocspec.org/v0.20.1/dsds.bundled.schema.json
schemaVersion: "0.20.1"
name: Acme Design System
entries:
  - kind: component
    id: button
    name: Button
    description: A clickable control that triggers an action.
    metadata: { status: stable }
    sections: []
```

(This YAML is the *spec's* shape, shown for reference only — not something a
zeroheight customer authors. The prior 0.12.0 root keys `dsdsVersion` and
`entity`/`entityGroups` no longer exist.)

Large documents split across files with a `ref` of `rel: file`; the `rel: source`
ref names an entry's primary source. `$extensions` (see below) can appear at
document, entry, section, or item level.

## Entry kinds

An entry's `kind` is one well-known value or a namespaced custom one
(`com.acme.icon-library`). The well-known kinds:

| kind | what it documents |
| --- | --- |
| `system` | the design system as a whole — its overview, principles, getting-started. One per document, conventionally first. |
| `component` | a reusable UI element (button, input, modal). Adds `traits`, `combos`, `sourceFiles`, `imports`, `specs` on top of the common envelope. |
| `token` | a design token or token grouping. Carries `tokenType` and a required `source` pointer to the DTCG file; `combos` for pairing rules. |
| `theme` | a named token override set (dark, high-contrast, brand). Carries `colorScheme` and a required `source` (DTCG). |
| `entry` | the generic kind — **foundations, patterns, guides, glossaries, and anything else** live here. No scoped fields beyond the common envelope; its sections carry the meaning. |

> 0.12.0's separate `foundation`, `pattern`, `guide`, and `token-group` kinds are
> **gone**. Foundations/patterns/guides are now `entry`; token groupings are `token`
> entries grouped via `metadata.group`. Auditing for those old kinds is a spec error.

## Common entry envelope

Every entry shares:

- `id` — **the machine key, everywhere.** (0.12.0 called this `identifier`.) Traits,
  trait values, section items, combos — the stable key is always `id`; `name` is only
  ever a human display label. This id-vs-name distinction is the single most common
  thing a human-authored docs page lacks.
- `kind` — the entry type (above).
- `name` — human display name (required).
- `description` — one-line purpose statement (required).
- `purpose` — optional longer "why it exists".
- `metadata` — optional facts (see below).
- `sections` — the documentation body (see below).
- `extends` / `related` / `refs` — pointers to other entries and external material
  (see the refs model below).
- `$extensions` — namespaced tool escape hatch.

### metadata

`status` (single object or an array, one per platform), `since`, `group`
(e.g. `"color.action"`), `aliases`, `preview`, `context`, `owner` (RFC 5322),
`updated` (`{date, note}`), `reviewed` (`[{date, by, note}]`), `tags`.

A **status item** is `{ platform, status, since?, deprecationNotice?, note? }`.
`status` is an open string (`experimental` / `stable` / `deprecated` / …);
`deprecationNotice` is required when `status` is `deprecated`. (There is no `value`
key and no `overall` key — 0.12.0's status shapes are replaced by this per-platform
array or a single object.)

## Sections and the `for` audience field

`sections` replaces 0.12.0's `documentBlocks`. Each section is a typed object:

- `kind` — `guidelines`, `definitions`, `steps`, `section` (generic), or a custom
  namespaced kind. **Any entry kind can use any section kind** — nothing restricts
  which pairs with which.
- `for` — **required** audience: `all` (default reading, serves people and agents),
  `human` (people only), or `agent` (agent-only notes).
- `title`, `description` — optional heading and intro.
- `context` — optional job tag that sub-classifies a section (`anatomy`, `terms`,
  `keyboard`, `events`, or custom). E.g. a `definitions` section with
  `context: anatomy` documents component parts.
- `items` — the structured content array (shape depends on `kind`).
- `freeform` — nestable prose with headings, for anything not structured.
- `metadata`, `$extensions`.

### `for: agent` replaces `agentDocumentBlocks`

0.12.0 had a separate `agentDocumentBlocks` array for agent-only content. In 0.20.x
that is just a **section with `for: agent`** — same section kinds, flagged for
agents. It is the home for firm ready-to-act notes that would clutter human docs:
hard MUST / MUST-NOT rules, look-alike disambiguation, runnable checks. Tools don't
show `for: agent` sections to humans. A docs system that wants to be genuinely
agent-ready uses this audience — its absence is a legitimate, high-value audit
finding for systems targeting AI consumption.

## Section kinds in detail

### `guidelines` — usage rules with rationale (and the verification model)

A `guidelines` section's `items` are guideline objects. Each carries:

- `level` — **required** RFC 2119 strength: `must` / `should` / `may` /
  `should-not` / `must-not`. (0.20.x adds `may`.) Agents treat `must` / `must-not`
  as hard limits.
- `statement` — the concrete rule text (omittable only if pointing elsewhere via a
  `same-as` / `external-link` ref).
- `framing` — `how-to-use` (default) or `when-to-use`. `when-to-use` items are the
  recommend/discourage guidance that 0.12.0 modelled as `useCases` with a `stance`.
- `example` — an illustrative example (must reflect the `level`).
- `alternatives` — refs to a better option (the "use Link instead" pointer that
  0.12.0 modelled as `alternative.identifier`).
- `evidence` — refs to external standards (WCAG, MDN).
- `checks` — refs to verification, `rel: test` or `rel: lint-rule`.
- `checkedBy` — `automated` / `assisted` / `manual`. **This plus `checks` replaces
  0.12.0's separate `criterion` + `verification` + `check`.** A guideline with a
  testable `statement`, a `checkedBy` mode, and (where automatable) a `checks` ref is
  the highest rung of machine-readability — "enforceable, testable guidance" — and
  usually the biggest gap in existing docs.
- `related`, `refs`, `tags`.

### `definitions` — terms, anatomy, props, glossary

Term/definition items (`context: anatomy` for component parts, `context: terms` for
a glossary, and so on). This is where component **anatomy**, **prop/API tables**,
and keyboard/event references live in 0.20.x — as structured `definitions` items,
each with a stable `id`, rather than the 0.12.0 `anatomy`/`api` blocks.

### `steps` — ordered/unordered procedures or checklists

Procedure steps or checklists (the getting-started / migration / contribution
content that 0.12.0 put in a `guide` entity's `steps` block).

### `section` — generic

The catch-all: `items` and/or `freeform` for prose that doesn't fit a typed kind.

## Component specifics

Beyond the envelope, a `component` entry adds:

- `traits` — **replaces 0.12.0's `variants` and `states` blocks.** Each trait is
  tagged by `kind`:
  - `boolean` — an on/off flag (`{ kind: boolean, id, description, setBy? }`).
  - `enum` — named options (`{ kind: enum, id, name, description, values[], setBy?, refs? }`);
    the **first value is the default**. Each value is `{ id, description, name?, purpose?, examples?, since? }`.
  - `setBy` is `consumer` or `component` (who sets it — a prop vs an internal state
    like hover/focus/disabled).
- `combos` — pairing rules between traits (or a trait and a token). A combo is
  `{ subject, items[], level, note? }` where `level` is an RFC 2119 level saying
  whether the pairing is allowed/denied and how strictly. (`subject` may be a token
  reference like `{color.action.primary}`.)
- `sourceFiles` — `[{ platform, file }]`, the source a tool extracts an API from.
- `imports` — `[{ platform, code, package? }]`, how to import the component.
- `specs` — refs to an already-extracted machine-readable API contract (a Custom
  Elements Manifest, DS Contracts doc, …). DSDS points at it; it doesn't parse it.

## Token / theme specifics

- A **token** entry needs `tokenType` (a DTCG type such as `color`, `dimension`) and
  a **required `source`** ref to the DTCG file. No `source` = not machine-traceable
  to a value. Groupings use `metadata.group` (no more `token-group` kind or
  `children[]`).
- A **theme** entry needs `colorScheme` (`light` / `dark`, default `light`) and a
  **required `source`** ref to the DTCG file that holds the override values. The
  named token overrides live in DTCG, not inline (no more `theme.overrides[]`).

## The refs (pointer) model

Relationships are first-class in 0.20.x, via `refs` (and the convenience fields
`extends`, `related`, `alternatives`, `evidence`, `checks`, `specs`, `source`). A
ref targets either:

- `to` — inside this document's graph: an entry `id`, or `entryId#itemId` to point
  at one item.
- `href` — outside: a URL, relative file path, or pseudo-scheme (`npm:@org/ds`).
- or a bare string shorthand for an external link/file.

Common `rel` values: composition (`depends-on`, `composes`, `part-of`,
`implements`), alternatives (`alternative-to`, `replaces`, `extends`), reference
(`same-as`, `refines`, `relates-to`, `see-also`), verification (`test`,
`lint-rule`), external (`file`, `source`, `design`, `storybook`, `package`,
`external-link`, `contract`), pairing (`pairs-with`, `excludes`). This replaces
0.12.0's `metadata.links` and the component `links` array.

## $extensions

A namespaced escape hatch (`com.figma`, `org.w3c`, …) for tool-specific data,
allowed at document / entry / section / item level. Convention: include a `context`
key explaining the tool's purpose. Lets tools attach data without forking the schema.

## Conventions that catch authors (worth checking in an audit)

- `id` not `name` for every machine key (traits, values, section items, combos).
- Guideline `level` is required and now includes `may`; recommend/discourage
  guidance is `framing: when-to-use`, not a separate block.
- `checkedBy` (+ `checks`) is how a guideline becomes testable — not a separate
  `criteria` entity.
- Components use `traits` (boolean/enum, first enum value is default), not
  `variants`/`states` blocks; anatomy/props are `definitions` sections.
- Tokens and themes **must** carry a `source`; values live in DTCG.
- Agent-only content is a section with `for: agent`, not a separate array.
