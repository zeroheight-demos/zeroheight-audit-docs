---
description: Push the fixes from a DSDS audit into zeroheight as drafts for someone to review. Needs the zeroheight MCP's write tools (login connection, editor or admin).
argument-hint: "[which fixes, e.g. \"just P1\" or \"just Button\"]"
---

Push the fixes from the **dsds-audit** report into the user's zeroheight styleguide,
saving each page's fixes as a **draft**. Nothing is published unless the user asks.

Scope for this run (optional, may be empty): $ARGUMENTS

Follow `skills/dsds-audit/reference/push-fixes.md` exactly:

1. Confirm the zeroheight MCP's **write tools** are available (`edit-page`,
   `get-draft-changes`, `merge-draft`). If they aren't, stop: tell the user to
   reconnect with the login URL `https://mcp.zeroheight.com/mcp` as an editor or
   admin (see the plugin README), and point them at the paste pack meanwhile.
2. Find the audit to push. Use the report from this conversation, or ask for the
   report file. If there's no audit yet, run `/audit-docs` for the same scope first.
3. Agree the scope with the user (default: [Author] fixes, P1 first, plus drafted
   For Agents tabs) and list the pages and moves before writing anything.
4. For each page: check for someone else's open draft, read the page fresh with
   `format: "editor"`, and apply that page's snippets as small `edit-page` changes
   saved to its draft.
5. Mark what was pushed in the report and summarise the drafts. Publish
   (`get-draft-changes`, then `merge-draft`) only when the user asks.
