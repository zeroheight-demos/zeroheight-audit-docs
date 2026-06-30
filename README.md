# zeroheight Audit Docs

Audit your **zeroheight** design system's documentation for machine-readability
against the **Design System Documentation Spec** (DSDS, [designsystemdocspec.org](https://designsystemdocspec.org)),
pinned to **0.12.0**. The audit reads your live styleguide through **your own**
zeroheight MCP connection and returns a prioritised, page-by-page list of authoring
moves plus ready-to-paste segments.

> Built on the **Design System Documentation Spec** by [PJ Onori](https://pjonori.blog) —
> an open, machine-readable format for design system docs. This plugin audits zeroheight
> docs against it; it does not define the spec. Read the spec at
> [designsystemdocspec.org](https://designsystemdocspec.org) (source:
> [somerandomdude/design-system-documentation-schema](https://github.com/somerandomdude/design-system-documentation-schema)).

## Install

Install from this repo as a plugin marketplace. Use whichever surface you work in.

### Claude desktop app (Cowork)

1. Open the **Plugins** panel (the **+** menu → **Plugins** → **Manage Plugins**).
2. Choose **Add → **Marketplace** → **Add from a repository** and enter the repo:
   `zeroheight-demos/zeroheight-audit-docs`
3. Find **zeroheight-audit-docs** in that marketplace and click **Install** (then
   enable it if it isn't enabled automatically).
4. When prompted, paste your personal **zeroheight remote MCP URL** — see
   [Connect your own zeroheight instance](#connect-your-own-zeroheight-instance-required)
   below.

### Terminal (Claude Code CLI)

```
/plugin marketplace add zeroheight-demos/zeroheight-audit-docs
/plugin install zeroheight-audit-docs@zeroheight-dsds
```

> The `/plugin` slash command is **terminal-only** — it isn't available in the
> desktop app. In the desktop app, use the Plugins panel steps above instead.

## Connect your own zeroheight instance (required)

This plugin does **not** ship a shared connection. Each user connects their own
zeroheight workspace — your URL is unique to you and carries your own credential, so
your docs are only ever read through your account.

When you install/enable the plugin, you're **prompted for one value** —
your *zeroheight remote MCP URL*. No file or shell editing.

1. Get your personal URL from **`zeroheight.com/settings/user/mcp`** (or any
   styleguide's *Remote MCP details* block — it renders each viewer's own URL).
2. Paste it into the **zeroheight remote MCP URL** field when the plugin's
   configuration dialog appears on enable.

That's it — the bundled `zeroheight` MCP server picks it up automatically (it
expands `${user_config.zeroheightMcpUrl}` in the plugin's `.mcp.json`). Your value is
stored locally in `settings.json` under
`pluginConfigs["zeroheight-audit-docs@zeroheight-dsds"].options`. To change it later, disable
and re-enable the plugin (`/plugin`) to get the prompt again, or edit that key
directly.

> Using local/token-based MCP instead of the remote URL? This plugin bundles the
> remote (URL) connection only. Connect zeroheight's local server separately if your
> org requires it.

## Use

```
/audit-docs
/audit-docs just Components
```

Or just ask in natural language — e.g. "audit our design system docs for
machine-readability" — and the `dsds-audit` skill triggers on its own.

## What's in here

- `skills/dsds-audit/` — the audit skill (procedure, pinned DSDS model, authoring
  patterns, rubric, report + paste-pack templates).
- `commands/audit-docs.md` — the `/audit-docs` entry point.
- `.mcp.json` — the bundled, templated zeroheight remote MCP server.
- `.claude-plugin/plugin.json` — plugin manifest.
- `.claude-plugin/marketplace.json` — marketplace listing (this repo is also the
  marketplace).

## Versioning

The plugin version tracks the **DSDS version it audits against** (currently
**0.12.0**). When the pinned spec is bumped, re-snapshot `skills/dsds-audit/reference/`
and re-tag the plugin to match.
