---
mode: agent
description: Push the fixes from a DSDS audit into zeroheight as drafts for someone to review. Needs the zeroheight MCP's write tools (login connection, editor or admin).
---

# Push DSDS audit fixes into zeroheight

Take the fixes from a **DSDS audit** report (from `/dsds-audit`) and apply them to
the user's zeroheight styleguide, saving each page's fixes as a **draft**. Nothing
is published unless the user asks.

Which fixes (optional, e.g. "just P1" or "just Button"; otherwise agree the scope
with the user first): `${input:scope}`

## Prerequisites

1. The **zeroheight MCP's write tools** must be available: `edit-page`,
   `get-draft-changes`, `merge-draft`. They come with the **login** connection
   (`https://mcp.zeroheight.com/mcp`) for editors and admins. If you only see the
   read tools, **stop** and tell the user to reconnect with the login URL (see
   [`COPILOT.md`](../../COPILOT.md)). The paste pack still works meanwhile.
2. The audit report. Use the one from this chat, or ask for the report file. If
   there isn't one, run `/dsds-audit` for the same scope first.
3. Read [`skills/dsds-audit/reference/push-fixes.md`](../../skills/dsds-audit/reference/push-fixes.md)
   before writing anything. It is the procedure; follow it exactly.

## In short

1. Agree the scope (default: [Author] fixes, P1 first, plus the drafted For Agents
   tabs) and list the pages and moves before writing.
2. Per page: `get-draft-changes` (skip pages someone else has a draft open on),
   `get-page` with `format: "editor"`, then one `edit-page` call with small
   find/replace changes, saved to the draft. Never pass `draft: false`.
3. Mark what was pushed in the report and summarise the drafts.
4. Publish (`get-draft-changes`, show the diff, then `merge-draft`) only when the
   user asks.
