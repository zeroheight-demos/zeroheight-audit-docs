# DSDS 0.12.0 — frozen reference model

This is a pinned, condensed snapshot of the Design System Documentation Spec
(DSDS) **0.12.0 draft**, captured for reproducible audits. The canonical source
is https://designsystemdocspec.org/ and the JSON Schema at
https://github.com/somerandomdude/design-system-documentation-schema
(`spec/schema/dsds.bundled.schema.json`). DSDS is a draft and will change — when
you bump the version this skill targets, re-snapshot this file and update the
rubric, then re-tag the plugin.

> Audit against the version recorded here, not against whatever is live. A moving
> target makes audits non-reproducible, and customers need stable results they
> can act on over several sprints.

## What DSDS is (and isn't)

DSDS is a JSON format that documents the *how and why* of a design system in a
machine-readable shape — components, tokens, themes, foundations, patterns, and
guides. It does **not** carry token *values*; that is the job of the W3C Design
Tokens (DTCG) format, which DSDS links to via a token's `source`. An audit that
flags "missing hex values" is misreading the spec — DSDS references values, it
does not store them.

The whole point is one source of truth that serves three readers: humans,
parsers, and agents. Machine-readability means a parser or agent can reliably
extract structure (types, levels, identifiers, relationships, testable criteria)
without scraping prose.

## Document shape

A DSDS file requires `dsdsVersion` and one of:

- `entity` — a single entity per file (the `kind` field names its type).
- `entityGroups` — several entities in one file, or a manifest of `$ref`s to
  single-entity files.

```json
{
  "$schema": "https://designsystemdocspec.org/v0.12.0/dsds.bundled.schema.json",
  "dsdsVersion": "0.12.0",
  "entity": { "kind": "component", "identifier": "button", "name": "Button",
    "description": "...", "metadata": { "status": "stable" },
    "documentBlocks": [] }
}
```

`systemInfo` (e.g. `systemName`) sits at the root of a multi-entity document.

## Entity kinds (seven)

| kind | what it documents |
| --- | --- |
| `component` | a reusable UI element (button, input, modal). Accepts anatomy, api, variants, states, design-specifications + general blocks. |
| `token` | a single design token. Carries `tokenType` and a `source` pointer to the DTCG file. General blocks only. |
| `token-group` | a nested tree of tokens (`children` may hold tokens or groups). General blocks only. |
| `theme` | a named set of token `overrides` (dark, high-contrast, brand). Lists which token names change; DTCG holds the values. |
| `foundation` | a design base (color, type, spacing, elevation). Accepts principles, scale, motion + general blocks. |
| `pattern` | a multi-component solution to a user need (forms, empty states). Accepts interactions, anatomy, variants, states + general blocks. |
| `guide` | a long-form reading document (getting-started, contribution, migration). Accepts steps, imports + general blocks. |

## Common entity envelope

Every entity shares: `kind`, `identifier`, `name`, optional `description` and
`metadata`, plus `documentBlocks` and optional `agentDocumentBlocks`. (Tokens are
the exception — they key off `tokenType`/`source` rather than rich prose.)

- **`identifier` is the machine key, everywhere.** API properties, anatomy parts,
  criteria, variants, states — the stable key is always `identifier`. `name` is
  only ever a human display label. This distinction is the single most common
  thing a human-authored docs page lacks.
- **`metadata`** carries `status`, `category`, `tags`, `links`, `aliases`,
  `since`, `last-updated`, `preview`/`thumbnail`.
- **`status`** has exactly two shapes: a bare string (`"stable"`) or an object
  with `overall` and optional per-`platforms` tracking. There is no `value` key.

## documentBlocks vs agentDocumentBlocks

`documentBlocks` is the default home — what a person reads; agents read it too.
`agentDocumentBlocks` is an optional, agent-only array using the same block kinds,
for firm ready-to-act notes that would be clutter for people: hard MUST / MUST-NOT
rules, look-alike disambiguation, and runnable checks. Tools never show it to
humans. A docs system that wants to be genuinely agent-ready uses this layer —
its absence is a legitimate, high-value audit finding for systems targeting AI
consumption.

## The verification model (criteria)

A `guideline` pairs `guidance` + `rationale` + an RFC 2119 `level`
(`must` / `should` / `should-not` / `must-not`). Agents treat must / must-not as
hard limits.

A `criterion` turns a guideline into something checkable: a stable `identifier`
plus a testable `statement`, an RFC 2119 `level`, and a `verification` mode:

- `automated` — a runner decides; names a `check` (its `scheme` + config).
- `assisted` — a tool surfaces candidates, a human decides.
- `manual` — pure human judgement; `techniques`/`failures` are the procedure.

When `verification` is omitted, tools must not assume the criterion is
automatable. `examples` on a criterion are fixtures, each with an `outcome`
(`pass`/`fail`). The `statement` is the source of truth; the `check` is its
executable shadow.

This criteria layer is what separates "documented guidance" from "enforceable,
testable guidance" — the highest rung of machine-readability and usually the
biggest gap in existing docs.

## Conventions that catch authors (worth checking in an audit)

- `identifier` not `name` for every machine key.
- `status` has only the two shapes above — no `value` property.
- `tokens` on a component is a **purpose-keyed map**, not an array:
  `{ "text-color": "color-button-fg", "background": "color-button-bg" }`.
- Variants are **flag** (boolean on/off) or **enum** (named options); a
  single-value enum is valid.
