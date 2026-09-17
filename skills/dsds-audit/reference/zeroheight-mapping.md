# Mapping zeroheight pages to DSDS

How a real zeroheight page (returned by `get-page` as Markdown) maps onto DSDS
entries and sections. This is the translation layer the audit reasons over: it
lets you say "this `## States` section *is* your states/traits content, it just
isn't structured yet" instead of "you have no states section."

## Step 1 — classify the page to a DSDS entry kind

Use navigation location first, then page content, to pick the `kind`. 0.20.x has a
small kind set — most non-component/token/theme pages are the generic `entry`:

| zeroheight signal | DSDS `kind` |
| --- | --- |
| under **Components**, documents one UI element with anatomy/variants/states | `component` |
| a token catalogue / "About Design Tokens" listing token names + values | `token` (one entry, or several, grouped via `metadata.group`) |
| a **Theming** page naming token overrides per mode | `theme` |
| the styleguide's overview / home / "About this system" / principles hub | `system` |
| under **Foundations** (color/type/spacing/motion/elevation), under **Patterns** (multi-component solutions), or a guide (intro / getting-started / contribution / changelog / "Using the MCP") | `entry` (generic) |

0.12.0's `foundation`, `pattern`, `guide`, and `token-group` kinds no longer exist:
foundations, patterns, and guides are all the generic **`entry`** kind (the sections
carry what they document), and token groupings are `token` entries. Introductions,
changelogs, and "welcome" pages are legitimately generic `entry`s (or the `system`
entry) — don't audit them as components. Flag the classification you chose so a
human can correct it; ambiguous pages (e.g. half foundation, half token catalogue)
should be called out, not silently bucketed.

## Step 2 — map page sections to DSDS sections

zeroheight section headings vary by team, so match on intent, not exact title.
Typical Markdown headings seen in the wild and their DSDS target (section `kind` +
`context`, or a scoped field):

| zeroheight section (typical headings) | DSDS target | usual state when first audited |
| --- | --- | --- |
| Overview, "Use X when…", When to use / when not to | `guidelines` items with `framing: when-to-use` | Partial — prose, no `level`, no `alternatives` ref |
| Anatomy (annotated diagram + numbered parts) | `definitions` section, `context: anatomy` | Partial — parts named in prose, no `id`s |
| Variants, Component properties table, By hierarchy / By shape | component `traits` (enum/boolean); props → `definitions` | Partial — table is human-readable, not keyed traits |
| States (+ state screenshots with notes) | component `traits` (`setBy: component`) | Partial — states named, no `id`s |
| Guidelines, Do / Don't, Best practice | `guidelines` (each item an RFC 2119 `level`) | Partial — no `level` on each item |
| Accessibility (contrast, focus, touch target, button-vs-link) | `guidelines` + `checkedBy`/`checks` | Partial → Pass when tied to a `checks` ref + `checkedBy` |
| Content / Content guidelines (label rules, do/don't) | `guidelines` (content) | Partial — itemise the do/don't pairs |
| Code / Props table / Storybook link | `definitions` (props) or component `specs`/`sourceFiles` ref | Partial — declare the props table or Storybook URL as the machine source |
| Tokens & Variables, "Tokens used" tables | token `source` (DTCG); component `combos`/`refs` to tokens | Partial — tokens shown as a table, not sourced by name |
| Theme support / Theming | `theme` entry (`colorScheme` + `source`) or a `refs` link | Partial |
| Layout & spacing, Touch target | `guidelines` / `definitions` | Partial |
| Principles (system/foundation) | `guidelines` or a `section` with titled items | Partial |
| Spacing/type scale, ramps | `definitions` / structured `section` items | Partial |
| Motion / easing / duration | structured `section`/`definitions` items | Partial |
| Changelog | `metadata.updated`/`reviewed` + a generic `entry`, not a section | n/a |
| Figma embeds, design callouts | `refs` (`rel: design`) / `metadata.preview` | n/a — these are ref/link metadata |

## Step 3 — recognise the recurring gaps

Across well-written zeroheight pages, the content is usually *present and good*;
what's missing is the machine structure. The recurring, high-value findings:

1. **No stable `id`s.** Pages have display names ("Primary", "Hover") but no
   machine keys. This blocks almost every downstream parser/agent use.
2. **Guidance lacks RFC 2119 levels.** Rich do/don't prose, but nothing tells a
   parser which rules are `must` vs `should` (vs the new `may`).
3. **When-to-use lacks framing + alternatives.** "Use a link instead" is written
   for humans but not encoded as a `framing: when-to-use` guideline with an
   `alternatives` ref to the other component.
4. **Props/traits are tables, not typed.** A Markdown props table is readable but
   isn't a `definitions` section, and variants/states aren't keyed `traits`;
   declare a `source`/`specs` ref (Storybook/CEM) or structure them.
5. **No testable checks.** Accessibility and usage rules are narrative; no
   guideline carries `checkedBy` or a `checks` ref (e.g. axe-core).
6. **No `for: agent` section.** Nothing in the agent-only audience — no hard
   MUST/MUST-NOT, no look-alike disambiguation (button vs link vs icon-button).
7. **Tokens shown, not sourced.** Token tables list names + values inline rather
   than a `token` entry with a `source` pointer to the DTCG file.

These seven are the backbone of most reports. Frame each as a concrete, located
change on the specific page — never a generic "add identifiers."

## Note on fidelity

`get-page` returns rendered Markdown, not the page's underlying structure, and
image annotations are coordinate metadata only (you can list anatomy labels but
not infer what each points at without fetching the image). Audit what the text
asserts; where a determination needs the visual, say so rather than guessing.
