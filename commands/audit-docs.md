---
description: Audit a zeroheight design system's docs for machine-readability against DSDS 0.12.0 and produce a prioritised, page-by-page list of authoring moves plus ready-to-paste segments.
argument-hint: "[styleguide or page scope, e.g. \"just Components\"]"
---

Run the **dsds-audit** skill to audit the user's zeroheight design system documentation
against the Design System Documentation Spec (DSDS, pinned to 0.12.0).

Scope for this run (optional, may be empty): $ARGUMENTS

Follow the skill's procedure exactly:

1. Confirm the **zeroheight MCP is connected** (tools `list-pages`, `search-pages`,
   `get-page`, `list-releases`, `get-page-asset`). If it is not, stop and tell the user
   to set their personal zeroheight remote MCP URL — see the plugin README. Do not
   substitute a generic checklist.
2. Read all six source files in the skill (`reference/` + `templates/`) before auditing.
3. If a scope was given above, audit that subset; otherwise call `list-pages` and confirm
   scope with the user before auditing everything.
4. Produce the two deliverables the skill specifies: the Markdown audit report and the
   companion `paste-pack.html`. Omit `[Platform]` findings entirely.
