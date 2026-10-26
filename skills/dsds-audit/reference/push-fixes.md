# Pushing the fixes into zeroheight

The audit's ready-to-paste snippets are the fixes, already written. With write
access to zeroheight, the agent can apply them itself: each page gets its fixes
saved as a **draft**, someone reviews the drafts in zeroheight, and nothing goes
live until a person publishes it. The paste pack stays the route for anyone
without write access, and for anything the agent can't push.

Read this file only when the user wants the fixes pushed. The audit itself never
writes.

## 1. Check you can write

Pushing needs the zeroheight MCP's write tools: `edit-page`,
`get-draft-changes`, `merge-draft` and `update-page-metadata`. They come with the
**login** connection (`https://mcp.zeroheight.com/mcp`, or the zeroheight
connector in Claude), for **editors and admins**.

If you can only see the read tools, the connection is read-only (a personal link
URL, or a viewer account). Stop and tell the user: to push fixes, reconnect using
the login URL and sign in as an editor or admin. Until then, the paste pack works.

Writes always go to the current work-in-progress release. If the audit read an
older release (a `releaseId`), page ids differ: call `list-pages` without a
`releaseId` and match pages by name before writing anything.

## 2. Agree what to push

Offer a sensible default and let the user narrow it:

- **Default:** every **[Author]** finding that has a drafted snippet, P1 first,
  plus the **For Agents** tabs you drafted.
- **[New content]** fixes (acceptance criteria, when-not-to-use guidance, anything
  you wrote rather than restructured) only if the user says yes. They're drafts
  either way, but someone has to check the substance, so call them out.
- Let them scope it: "just P1", "just Button", "only the For Agents tabs".

Then list the pages and the moves you'll make on each, and wait for a yes.

Never push:

- anything that fills an empty page, unless the user gives you the content
- deletions of pages or sections
- moving or hiding pages, unless the user asks (those apply straight away, not to
  a draft)
- a publish. Pushing means drafts. Publishing is step 5, and only on request.

Once per run: `read-resource` on `zeroheight://editor-instructions` (or read the
resource directly if your client can) and `get-tone-of-voice` for the styleguide.
Write every word you add in that tone.

## 3. For each page

1. **Check for someone else's draft.** Call `get-draft-changes`. An error means
   the page has no open draft, so carry on. If there is one and anyone with
   `is_you: false` has edited it, skip the page and tell the user who's working
   on it: don't pile your edits onto someone else's draft. If you're the only
   contributor (an earlier push, say), carry on.
2. **Read the page fresh.** `get-page` with `format: "editor"` for the content and
   its `version_id`. Check each finding still holds, since the page may have
   changed since the audit. Drop any fix that no longer applies.
3. **Turn each snippet into small find/replace changes** (table below), all in
   **one** `edit-page` call for the page. Copy every `find` exactly from the XML you
   just read, with just enough context to match once. `block-id`s make good
   anchors. Never `find` the whole page.
4. **Keep everything you aren't changing.** Preserve `block-id`, `uid`, `uuid` and
   `id` on existing content, leave them off new content, and copy every
   `<pm-node>` back exactly as it was.
5. **Send it** with `edit-page`, passing the `version_id`. Don't pass `draft`, so
   it saves to the page's draft. If it's rejected as invalid, the server lists every
   problem at once: fix them all and retry once. On a version conflict, read the
   page again and rebuild the changes. If it still fails, skip that move, keep
   its paste-pack snippet and note why.

### Audit move → edit

| Audit move | The edit |
|---|---|
| Fill the introduction | Add `<zh-intro><p>…</p></zh-intro>` as the first element inside `<zh-doc>` (plain text with bold and italics only, no links). If there's one already, change its text and keep its `block-id`. |
| Guidance as prose → guidelines table with a Level column | Replace the paragraph or list blocks holding those rules with `<table show-header="true">`: Guideline, Level, Why. Keep the page's own wording in the Guideline column, and use the level vocabulary the audit recommended. |
| Do's and don'ts as prose | Replace them with a `<rules>` block, one `<rule type="do">` or `type="dont"` per rule. |
| Consistent props / variants table | Rebuild the table with the standard columns (Prop, Type, Default, Required, Description, or Property, Type, Options, Default). Keep every value. Add nothing the page doesn't already say. |
| Name states / anatomy parts | Change the specific labels in the headings or table cells to the canonical name, everywhere it appears on the page. |
| When to use / when not to use | Add the headings and their content in the tab the snippet names. |
| Link the alternative | Wrap the component's name in `<a href="…">`, using that page's URL from `list-pages`. |
| Raw hex or pixel values → token names | `list-token-sets`, then `list-tokens` with `modeKey` set to the default mode. Write a `<token-mention/>` (`path`, `token-set-id`, `uuid`) only when the page names the token or exactly one token in the relevant collection has that value. Otherwise leave it and list it for a person. |
| Status vocabulary | Change the cells in the status table to the pinned vocabulary. |
| Add a For Agents tab | See below. |
| Rename a colliding title | `update-page-metadata` with the new `name`. On a page with an open draft the rename is saved to the draft, so do it after that page's content edits. On a page with no draft it goes live straight away, so ask first. |

### Adding the For Agents tab

- **The page already has tabs** (its content is `<doc-tab>` elements): add a new
  `<doc-tab name="For Agents">` after the last `</doc-tab>`, with no `uid`, and an
  `order` one higher than the last tab's.
- **The page has no tabs:** tabs and ordinary blocks can't sit side by side, so
  adding a tab means wrapping the existing content in a first tab. **Ask first.**
  The choice is between wrapping the current content in a tab (named the way the
  styleguide names its first tab elsewhere, such as "Usage") and adding For
  Agents as a section at the end of the page (`<heading level="2">For
  Agents</heading>` and its content) without restructuring anything.

Inside the tab:

```xml
<heading level="2">Hard rules</heading>
<ul><li>MUST NOT use Button to navigate to a URL. Use <a href="…">Link</a>.</li><li>MUST provide an accessible name.</li></ul>
<heading level="2">Disambiguation</heading>
<ul><li>vs <a href="…">Link</a>: Link navigates, Button performs an action on the page.</li></ul>
<heading level="2">Acceptance criteria</heading>
<tl><ti>icon-only-has-name: every icon-only button has a non-empty accessible name. Check: axe-core / button-name (automated)</ti></tl>
```

## 4. Report what you pushed

Update the Markdown report: mark each move **Pushed to draft** or **Not pushed**
with the reason, so the paste pack only has to cover what's left. Then tell the
user:

- each page with a new draft (link), and what changed on it
- what you skipped and why (someone else's draft, needed a decision, failed)
- the [New content] you wrote, which needs a person to check the substance
- that **nothing is live yet**: every change is a draft until someone publishes it

## 5. Review and publish (only when asked)

The user can compare each draft with the published page in zeroheight. Or you can
show the changes here: `get-draft-changes` for the page, then show the diff. If
anyone else edited the draft (`is_you: false`), name them and say their edits will
be published too. If the draft is in review, say who was asked to review it.

Publish only when the user says so, page by page or "all of them" once they've
seen every diff: `merge-draft` with the `merge_token` from the
`get-draft-changes` call you just showed. If the draft changed since, the merge is
refused: read the changes again and show them before retrying.

Afterwards, offer to re-run the audit on those pages so they can see the score
move. Before a merge, score the drafts by reading them with `format: "editor"`,
which returns the draft. The default viewer format returns the published page.
