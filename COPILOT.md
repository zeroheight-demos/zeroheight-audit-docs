# Using this audit with GitHub Copilot

This repo's audit is authored as a Claude Code plugin (`skills/`, `commands/`,
`.mcp.json`), but the substance — the DSDS model, authoring patterns, rubric, and
report templates under `skills/dsds-audit/reference/` and `templates/` — is plain,
tool-agnostic Markdown. This file adds a thin Copilot layer over the same content so
the audit runs in **GitHub Copilot** as well as Claude.

Nothing here duplicates the audit logic. The Copilot **prompt file** points at the
same six source files the Claude skill reads.

## What was added for Copilot

| File | Purpose |
|---|---|
| [`.github/prompts/dsds-audit.prompt.md`](.github/prompts/dsds-audit.prompt.md) | The audit prompt. Invoke it as `/dsds-audit` in Copilot Chat. Mirrors the `/audit-docs` slash command and reads the same reference files. |
| [`.vscode/mcp.json`](.vscode/mcp.json) | zeroheight MCP config for **VS Code** Copilot. Prompts you for your personal MCP URL on first use. |

The **Copilot coding agent** MCP config is not a committed file — it lives in
repository settings (see below).

## Prerequisite: your zeroheight remote MCP URL

Same requirement as the Claude plugin — each user connects their own zeroheight
workspace. Your URL is unique to you and carries your own credential, so your docs
are only ever read through your account.

Get it from **`zeroheight.com/settings/user/mcp`** (or any styleguide's *Remote MCP
details* block).

## Surface 1 — VS Code Copilot Chat (interactive)

1. Ensure MCP support is enabled in VS Code Copilot (`chat.mcp.enabled`; recent
   VS Code has it on by default).
2. Open this repo in VS Code. The committed [`.vscode/mcp.json`](.vscode/mcp.json)
   defines a `zeroheight` HTTP server. Start it (Command Palette → *MCP: List Servers*
   → Start, or the **Start** lens above the server in the JSON). VS Code will prompt
   for your **zeroheight remote MCP URL** and store it in encrypted secret storage —
   it is **not** written to the repo.
3. Open Copilot Chat in **Agent** mode and run:

   ```
   /dsds-audit
   /dsds-audit just Components
   ```

   Confirm the zeroheight tools (`list-pages`, `get-page`, …) appear in the agent's
   tool list before it starts; if not, the server isn't connected.

## Surface 2 — Copilot coding agent (autonomous, on GitHub)

The coding agent reads prompt files from the repo, but its MCP servers are configured
in **repository settings**, not a committed file.

1. On GitHub: **Repo → Settings → Copilot → Coding agent → MCP configuration**.
2. Paste this JSON, substituting your own URL (or reference a repo/org secret — the
   agent supports `COPILOT_MCP_*` secrets so you don't hardcode a credentialed URL):

   ```json
   {
     "mcpServers": {
       "zeroheight": {
         "type": "http",
         "url": "https://YOUR-PERSONAL-ZEROHEIGHT-MCP-URL"
       }
     }
   }
   ```

3. Assign the agent an issue/task that asks for the audit and points it at the prompt
   file, e.g. *"Run the `/dsds-audit` prompt to audit our zeroheight docs; scope: just
   Components."* The agent reads `.github/prompts/dsds-audit.prompt.md` and the six
   reference files, then opens a PR with the report and paste pack.

> The remote MCP is credentialed via the URL. Prefer a repo/org **secret** over
> pasting the raw URL into settings, and never commit the credentialed URL to the repo.

## What differs from the Claude experience

- **No auto-triggering.** The Claude skill invokes itself when you describe the task in
  natural language. In Copilot you invoke the prompt explicitly (`/dsds-audit`).
- **Reference loading is explicit.** The prompt file instructs the agent to read the
  six source files first (the Claude skill loads them progressively). Same files, same
  result.
- **Everything else is identical** — the DSDS 0.12.0 model, authoring moves, rubric,
  report structure, and paste-pack template are shared, unmodified.
