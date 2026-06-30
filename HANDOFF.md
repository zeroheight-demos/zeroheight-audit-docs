# Handoff — DSDS audit skill → plugin

Context for continuing this in Claude Code. The **skill is built and validated**;
what remains is wrapping it as a distributable plugin + marketplace for zeroheight
customers.

## What this is

A skill that audits a customer's **zeroheight** docs for machine-readability against
the **Design System Documentation Spec (DSDS, designsystemdocspec.org)**, pinned to
**0.12.0**, and outputs a prioritised, page-by-page list of changes plus ready-to-
paste segments.

## Current structure

```
skills/dsds-audit/
├── SKILL.md                                 # procedure
├── reference/
│   ├── dsds-0.12.0-model.md                 # pinned spec model (entities/blocks/criteria)
│   ├── entity-block-rules.md                # per-entity allowed blocks + structured shape
│   ├── zeroheight-mapping.md                # classify a page → DSDS entity; section→block
│   ├── zeroheight-authoring-pattern.md      # DSDS construct → zeroheight authoring move + For Agents tab spec
│   └── rubric.md                            # Pass/Partial/Missing, who-can-act, P1–P3, scoring
└── templates/
    ├── audit-report.md                      # report deliverable
    └── paste-pack.html                      # rendered copy-from-here deliverable
sample-audit-baseline.md                     # validated sample report (Baseline styleguide)
paste-pack-prototype.html                    # validated sample paste pack (paste-tested into zeroheight)
```

## Key design decisions (don't regress these)

- **Pinned to DSDS 0.12.0** for reproducibility. The spec is a draft; re-snapshot
  `reference/` and re-tag the plugin when bumping versions.
- **Audit against authoring moves, not raw JSON.** Customers can't author DSDS JSON
  in zeroheight; every finding is a move they can make in the editor, with the DSDS
  intent named. Source of truth: `zeroheight-authoring-pattern.md`.
- **Who-can-act tagging.** `[Author]` / `[New content]` appear in the report;
  `[Platform]` items are **omitted entirely** — no "for zeroheight" section anywhere.
- **For Agents tab** is the headline recommendation — hard rules + disambiguation +
  acceptance criteria; the seam for a future `agentDocumentBlocks` + `criteria`
  export. Always drafted when a page lacks one.
- **Two deliverables:** Markdown report (read) + `paste-pack.html` (copy from).
  zeroheight ignores raw Markdown on paste (reads only `text/plain`); the paste pack
  copies rendered `text/html` so formatting survives. Paste fills *content*;
  containers (tabs, callouts, Do/Don't, specimens) are made in the UI first.

## Remaining work (the plugin stage)

1. `.claude-plugin/plugin.json` — manifest (name, version tracking DSDS version,
   description). Keep `skills/dsds-audit/` where it is.
2. Bundle the **zeroheight MCP** config so install wires up the connection
   (`mcp/zeroheight.json` or equivalent). The skill depends on `list-pages`,
   `search-pages`, `get-page`, `list-releases`, `get-page-asset`.
3. Optional `commands/audit-docs.md` — a `/audit-docs` entry point for discovery.
4. A thin **marketplace** repo/manifest listing the plugin so customers add the
   marketplace and install in two commands.
5. Version the plugin to the DSDS version it audits against.
6. Decide whether to keep `sample-audit-baseline.md` / `paste-pack-prototype.html`
   as fixtures/examples or move them out of the shipped plugin.

## Still open (optional, pre-ship)

- Wider validation over a couple of input components (Select, Password Input) to
  confirm the props/variants snippets generalise beyond Button.
- Description-triggering optimization for the skill.

## Suggested first prompt in Claude Code

"Wrap the skill in `skills/dsds-audit/` as a Claude Code plugin: add the plugin
manifest, bundle the zeroheight MCP, add an `/audit-docs` command, and create a
minimal marketplace manifest that lists it. Follow the decisions in HANDOFF.md."
