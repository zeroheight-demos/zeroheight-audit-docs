# Changelog

All notable changes to the **zeroheight-audit-docs** plugin are recorded here, so
you can tell at a glance when it changes and what to re-pull.

The plugin **version tracks the DSDS version it audits against** (see the
[Versioning](README.md#versioning) note in the README). This log loosely follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); the audited DSDS version is
the headline for each release.

> **Updating:** releases don't auto-install. After a new version lands, refresh the
> marketplace and update the plugin — CLI: `/plugin marketplace update zeroheight-dsds`
> then update `zeroheight-audit-docs@zeroheight-dsds`; desktop (Cowork): Plugins panel
> → Manage Plugins → refresh the marketplace, then update the plugin. Your saved
> zeroheight MCP URL persists across updates.

## [0.20.2] - 2026-10-26

Still audits against **DSDS 0.20.1**. Adds pushing the audit's fixes straight into
zeroheight, now that the zeroheight MCP can write.

### Added
- **Push fixes into zeroheight.** After an audit, with the MCP's write tools
  available, the agent offers to apply the fixes itself: it agrees the scope (by
  default the [Author] fixes, P1 first, plus the drafted For Agents tabs), skips any
  page someone else has a draft open on, and saves each page's fixes to its
  **draft** with small `edit-page` changes. Nothing is published unless the user
  asks, and then only after showing `get-draft-changes`.
- `/push-fixes` command, and `/dsds-push-fixes` for GitHub Copilot.
- `skills/dsds-audit/reference/push-fixes.md`: the procedure, including how each
  audit move becomes an edit and how to add a For Agents tab to a page with or
  without tabs.

### Changed
- The audit offers to push once the report is done (skill step 8). The paste pack
  is still produced, for anyone without write access and for anything not pushed.
- The report marks each fix **Pushed to draft** or **Not pushed** with a reason.
- Docs: how to connect by login (`https://mcp.zeroheight.com/mcp`) to get the write
  tools, in the README and `COPILOT.md`.
- Versioning: the patch number now moves for plugin-only releases, so 0.20.x still
  means DSDS 0.20.1.

## [0.20.1] - 2026-09-17

Re-targets the audit at **DSDS 0.20.1** (from 0.12.0). 0.20.x is a full model
restructure, so the frozen reference model was re-snapshotted and the DSDS-intent
layer re-labelled across the skill. The customer-facing **zeroheight authoring moves
are unchanged** — only the spec conventions they map to.

### Changed
- Entities → **entries**: the seven kinds collapse to `system` / `component` /
  `token` / `theme` / generic `entry` (foundations, patterns and guides fold into
  `entry`; `token-group` is gone).
- `documentBlocks` / `agentDocumentBlocks` → **`sections`** with a
  `for: human | agent | all` audience field; block kinds become `guidelines` /
  `definitions` / `steps` / `section`.
- Component `variants` / `states` → **`traits`** (boolean / enum) + `combos`;
  anatomy / props move to `definitions` sections.
- `criteria` + `verification` → guideline **`checks` + `checkedBy`**.
- Guideline levels add **`may`**; machine key `identifier` → **`id`**; root keys are
  now `schemaVersion` + `name` + `entries`; a rich **`refs`** relationship model
  replaces `metadata.links`.
- Frozen model renamed `reference/dsds-0.12.0-model.md` → `reference/dsds-0.20.1-model.md`.

### Notes
- Guardrail made explicit: 0.20.x authors DSDS as **YAML**, but the audit never asks
  a zeroheight author to write YAML/JSON — it recommends the page move that yields the
  same machine-readable effect.
- Validated live against the Baseline styleguide; pages classify to the new kinds
  (including the new `system` kind) and findings frame in the new intent language.
- Shipped in [#1](https://github.com/zeroheight-demos/zeroheight-audit-docs/pull/1).

## [0.12.0] - 2026-06-30

Initial public release, auditing against **DSDS 0.12.0**.

### Added
- `dsds-audit` skill — audits a zeroheight styleguide against DSDS 0.12.0 and returns
  a prioritised, page-by-page list of zeroheight authoring moves plus ready-to-paste
  segments (a Markdown report + a `paste-pack.html` with formatting-preserving copy).
- `/audit-docs` command as the entry point.
- Bundled, templated `zeroheight` remote MCP server (`.mcp.json`), configured per user
  via the `zeroheightMcpUrl` prompt on install.
- **GitHub Copilot layer** (added 2026-07): `.github/prompts/dsds-audit.prompt.md`,
  `.vscode/mcp.json` and `COPILOT.md`, running the same audit from VS Code Copilot
  Chat and the Copilot coding agent.
- Install docs for the Claude desktop app (Cowork) and the Claude Code CLI.
