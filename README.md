# zeroheight Audit Docs

Audit your **zeroheight** design system's documentation for machine-readability
against the **Design System Documentation Spec** (DSDS, [designsystemdocspec.org](https://designsystemdocspec.org)),
pinned to **0.20.1**. The audit reads your live styleguide through **your own**
zeroheight MCP connection and returns a prioritised, page-by-page list of authoring
moves plus ready-to-paste segments.

With write access to zeroheight it can then **push those fixes into your styleguide**
as drafts: each page's fixes are saved to its draft for someone to review, and
nothing goes live until a person publishes it.

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

### To push fixes into zeroheight: connect by login

The audit only reads, so your personal URL is all it needs. Pushing fixes needs the
zeroheight MCP's **write tools**, which come with the **login** connection, for
**editors and admins**:

1. Use **`https://mcp.zeroheight.com/mcp`** as your zeroheight remote MCP URL (no
   token on the end) instead of your personal URL.
2. Sign in to zeroheight when you're prompted, and approve the connection. In Claude
   Code, if you aren't prompted, run `/mcp`, pick **zeroheight** and authenticate.
3. Check it worked: `edit-page`, `get-draft-changes` and `merge-draft` should now
   be in the zeroheight tool list.

Your organization's admin may need to turn on login access and write access first
(zeroheight → **Your organization** → **MCP**).

## Use

```
/audit-docs
/audit-docs just Components
```

Or just ask in natural language — e.g. "audit our design system docs for
machine-readability" — and the `dsds-audit` skill triggers on its own.

### Push the fixes

When the audit's done, and you're connected by login (see above), ask for the fixes
to be pushed, or run:

```
/push-fixes
/push-fixes just P1
/push-fixes just Button
```

The agent agrees the scope with you, skips any page someone else has a draft open
on, and saves each page's fixes to its draft. Review the drafts in zeroheight (or
ask to see the changes in chat), then publish them there, or ask the agent to
publish them once you're happy. It never publishes on its own.

## Using it with GitHub Copilot

The audit content is tool-agnostic Markdown, so it also runs in **GitHub Copilot** —
both VS Code Copilot Chat and the Copilot coding agent — via a thin wrapper
(`.github/prompts/dsds-audit.prompt.md` + `.vscode/mcp.json`) that reads the same
reference files. Setup and the invocation flow for both surfaces are in
[`COPILOT.md`](COPILOT.md).

## What's in here

- `skills/dsds-audit/` — the audit skill (procedure, pinned DSDS model, authoring
  patterns, rubric, report + paste-pack templates).
- `commands/audit-docs.md` — the `/audit-docs` entry point.
- `commands/push-fixes.md` — the `/push-fixes` entry point (pushes the audit's
  fixes into zeroheight as drafts; procedure in
  `skills/dsds-audit/reference/push-fixes.md`).
- `.mcp.json` — the bundled, templated zeroheight remote MCP server.
- `.claude-plugin/plugin.json` — plugin manifest.
- `.claude-plugin/marketplace.json` — marketplace listing (this repo is also the
  marketplace).
- `.github/prompts/dsds-audit.prompt.md`, `.github/prompts/dsds-push-fixes.prompt.md`,
  `.vscode/mcp.json`, `COPILOT.md` — the GitHub Copilot layer (same audit, same
  reference files; see `COPILOT.md`).

## Versioning

The plugin version tracks the **DSDS version it audits against**: the major and
minor numbers match the pinned spec (0.20.x audits **DSDS 0.20.1**), and the patch
number moves for plugin-only releases, such as 0.20.2 adding `/push-fixes`. When the
pinned spec is bumped, re-snapshot `skills/dsds-audit/reference/` and re-tag the
plugin to match. Each release's audited DSDS version is in [`CHANGELOG.md`](CHANGELOG.md).
