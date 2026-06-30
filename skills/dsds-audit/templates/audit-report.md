# DSDS Machine-Readability Audit — {{Styleguide name}}

> Audited against **DSDS {{version, e.g. 0.12.0}}** on {{date}}.
> Pages audited: {{n}}. Source: zeroheight styleguide "{{name}}".
> Every recommendation below is an **authoring move you can make in zeroheight**,
> and each page comes with **ready-to-paste segments**. Copy them from the companion
> **`paste-pack.html`** (open it in a browser, click *Copy formatted*) so they paste
> into zeroheight with formatting intact — pasting the raw Markdown here won't keep
> tables/headings.

## Executive summary

{{2–4 sentences: overall state, the single biggest systemic gap, the headline
number. Lead with blocker counts, not the average. Name the one move that would
move the needle most across the whole system — usually a convention like "adopt a
guidelines Level column everywhere" or "add a For Agents tab to every component".}}

| Metric | Value |
| --- | --- |
| Machine-readability score (avg) | {{xx%}} |
| P1 blockers | {{n}} |
| P2 core gaps | {{n}} |
| P3 enrichment gaps | {{n}} |
| Author-actionable findings | {{n}} ({{xx%}} of all) |
| Pages fully parseable today | {{n}} / {{total}} |

## System-level moves

Cross-cutting conventions — adopt once and most per-page findings collapse. Order
by impact. Each names the DSDS intent so the team understands the why.

1. **{{Move}} ({{P1/P2/P3}}, [Author]).** {{What to do system-wide, the DSDS intent
   it satisfies, and where they already do it (cite their own page) so it's a
   standardisation, not a new habit.}}
2. …

## Recommended sequence

A budget-ordered path; stop anywhere having done the highest-value work to date.

1. {{P1 wave — unblock parsing: fix duplicate titles, fill empty pages, reference
   tokens by name}}
2. {{P2 wave — standardise tables: Level columns, props/variants schemas, status
   vocabulary, linked alternatives, introductions}}
3. {{P3 wave — add the agent layer: For Agents tabs, acceptance criteria, tags}}

---

## Per-page findings

Repeat per page. Order findings P1 → P2 → P3. Only findings the customer can act on
appear anywhere in this report; platform-only items are omitted entirely.

### {{Page name}} — classified as `{{kind}}`

**Score: {{xx%}}** · P1: {{n}} · P2: {{n}} · P3: {{n}}
{{If classification is uncertain, say so and give the alternative.}}

| Check | Rating | Who | Severity | Authoring move (and DSDS intent) |
| --- | --- | --- | --- | --- |
| {{Title hygiene}} | {{Pass/Partial/Missing}} | Author | P1 | {{e.g. Rename "Checkbox 1" — it collides with "Checkbox". Unique titles act as stable identifiers.}} |
| {{Guidelines}} | Partial | Author | P2 | {{Move the dos/don'ts into a table with a Level column (Must/Should/Never), reusing your Color-page pattern. DSDS intent: RFC 2119 levels.}} |
| {{For Agents tab}} | Missing | New content | P3 | {{Add a "For Agents" tab: hard MUST/MUST-NOT rules + button-vs-link disambiguation + acceptance criteria. Reliably read via get-page; the seam for future agentDocumentBlocks export.}} |
| … | | | | |

**What's already good:** {{call out the Pass items and strong prose — the page is
closer than the score implies because the content exists; it needs the move, not a
rewrite.}}

#### Ready to paste

Drafted segments built from this page's own content. Copy one at a time into the
editor — not whole pages, just the specific block named. Update piece by piece.

**Paste into: {{Page}} › {{section, e.g. Guidelines}}** — {{what this fixes}}

```markdown
{{The actual filled-in segment — e.g. a guidelines table with the page's real rules
and Level values assigned. Real content, never placeholders.}}
```

**Paste into: {{Page}} › a new *For Agents* tab** — {{only when the page lacks one}}

```markdown
# For Agents

## Hard rules
- MUST {{real rule from this page}}
- MUST NOT {{real rule from this page}}

## Disambiguation (when NOT this component)
- vs {{look-alike}} — {{distinction; link the alternative's page}}

## Acceptance criteria
- [ ] {{slug-id}} — {{testable statement from this page}}. Check: {{tool/rule}}. ({{mode}})
```

{{Add one snippet per actionable finding that can be expressed as content. Skip
findings that are purely structural with nothing to draft (e.g. "fill the empty
page").}}

---

## Appendix A — For Agents tab coverage

A system-wide view of the highest-leverage move, since it's usually the biggest
gap. {{Which components have a For Agents tab, which don't; recommended rollout
order, e.g. highest-traffic components first.}}

| Component | For Agents tab | Hard rules | Disambiguation | Acceptance criteria |
| --- | --- | --- | --- | --- |
| {{Button}} | {{Missing}} | — | — | — |
| … | | | | |

## Appendix B — method

Audited each page via the zeroheight MCP (`list-pages` → `get-page`), classified to
a DSDS entity kind, mapped sections to the zeroheight authoring moves in the skill's
`reference/` files, and rated each applicable check Pass / Partial / Missing against
DSDS {{version}}. Scores exclude N/A. "Partial" means the information is present but
not via the authoring move, so a parser/agent can't reliably consume it.
