# Mapping zeroheight pages to DSDS

How a real zeroheight page (returned by `get-page` as Markdown) maps onto DSDS
entities and blocks. This is the translation layer the audit reasons over: it
lets you say "this `## States` section *is* your states block, it just isn't
structured yet" instead of "you have no states block."

## Step 1 — classify the page to a DSDS entity kind

Use navigation location first, then page content, to pick the `kind`:

| zeroheight signal | DSDS `kind` |
| --- | --- |
| under **Components**, documents one UI element with anatomy/variants/states | `component` |
| under **Foundations**, about color/type/spacing/motion/elevation | `foundation` |
| a token catalogue / "About Design Tokens" listing token names + values | `token-group` (with `token` children) |
| a **Theming** page naming token overrides per mode | `theme` |
| under **Patterns**, composing multiple components for a need | `pattern` |
| Intro / getting-started / contribution / changelog / "Using the MCP" | `guide` |

Introductions, changelogs, and "welcome" pages are legitimately `guide`s — don't
audit them as components. Flag the classification you chose so a human can correct
it; ambiguous pages (e.g. a page that is half foundation, half token catalogue)
should be called out, not silently bucketed.

## Step 2 — map page sections to blocks

zeroheight section headings vary by team, so match on intent, not exact title.
Typical Markdown headings seen in the wild and their DSDS target:

| zeroheight section (typical headings) | DSDS block | usual state when first audited |
| --- | --- | --- |
| Overview, "Use X when…", When to use / when not to | `useCases` | Partial — prose, no `stance` enum or `alternative` identifiers |
| Anatomy (annotated diagram + numbered parts) | `anatomy` | Partial — parts named in prose, no `identifier`s |
| Variants, Component properties table, By hierarchy / By shape | `variants` (+ `api` for the props table) | Partial — table is human-readable, not flag/enum typed |
| States (+ state screenshots with notes) | `states` | Partial — states named, no `identifier`s |
| Guidelines, Do / Don't, Best practice | `guidelines` | Partial — no RFC 2119 `level` on each item |
| Accessibility (contrast, focus, touch target, button-vs-link) | `accessibility` | Partial → Pass if tied to `criteria` with `verification` |
| Content / Content guidelines (label rules, do/don't) | `content` | Partial — itemise the do/don't pairs |
| Code / Props table / Storybook link | `api` | Partial — declare the props table or Storybook URL as the machine `source` |
| Tokens & Variables, "Tokens used" tables | `design-specifications` (component) or token `source` | Partial — tokens shown as a table, not the purpose-keyed map |
| Theme support / Theming | `theme` overrides or theme links | Partial |
| Layout & spacing, Touch target | `guidelines` / `design-specifications` | Partial |
| Principles (foundation) | `principles` | Partial |
| Spacing/type scale, ramps | `scale` | Partial |
| Motion / easing / duration | `motion` | Partial |
| Changelog | `metadata.last-updated` + a `guide`, not a block | n/a |
| Figma embeds, design callouts | `metadata.links` / `preview` | n/a — these are link metadata |

## Step 3 — recognise the recurring gaps

Across well-written zeroheight pages, the content is usually *present and good*;
what's missing is the machine structure. The recurring, high-value findings:

1. **No stable `identifier`s.** Pages have display names ("Primary", "Hover") but
   no machine keys. This blocks almost every downstream parser/agent use.
2. **Guidance lacks RFC 2119 levels.** Rich do/don't prose, but nothing tells a
   parser which rules are `must` vs `should`.
3. **useCases lack stance + alternatives.** "Use a link instead" is written for
   humans but not encoded as `stance: discouraged` + `alternative.identifier`.
4. **Props/variants are tables, not typed.** A Markdown props table is readable
   but isn't the `api`/`variants` block; declare its `source` (Storybook) or
   structure it.
5. **No `criteria` / verification.** Accessibility and usage rules are narrative;
   none are expressed as testable criteria with a `verification` mode.
6. **No `agentDocumentBlocks`.** Nothing in the agent-only layer — no hard
   MUST/MUST-NOT, no look-alike disambiguation (button vs link vs icon-button).
7. **Tokens shown, not sourced.** Token tables list names + values inline rather
   than a `token` entity with a `source` pointer to the DTCG file.

These seven are the backbone of most reports. Frame each as a concrete, located
change on the specific page — never a generic "add identifiers."

## Note on fidelity

`get-page` returns rendered Markdown, not the page's underlying structure, and
image annotations are coordinate metadata only (you can list anatomy labels but
not infer what each points at without fetching the image). Audit what the text
asserts; where a determination needs the visual, say so rather than guessing.
