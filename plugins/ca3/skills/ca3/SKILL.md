---
name: ca3
description: Use CA3 as Codex's user-owned context and memory layer. Trigger for continuing selected work, recalling prior decisions or material, and intentionally saving durable context across agents.
---

# CA3

CA3 gives AI agents a private, user-owned memory layer so important context can follow you across ChatGPT, Codex, Claude Code, and browser workflows.

Use CA3 when the user asks to continue selected work, remember, save, recall,
search, organize, or update persistent context, or when the current task may
depend on decisions or material saved by another agent.

## Source Of Truth

Do not infer detailed CA3 behavior from this file. The live MCP tool list and each MCP tool description are the source of truth for:

- Available tools.
- Required arguments.
- Scope-shaped visibility and resource boundaries.
- Notes, Current Context, Collections, and attachments behavior.
- Read, create, append, exact-edit, organize, and delete behavior.
- Scope and authorization failures.

If you need to know what CA3 can do, inspect the CA3 MCP tools exposed by the plugin instead of reading local plugin files or guessing from cached state.

## Agent Policy

Use proactive read, intent-bound write, and explicit delete:

- For "the Notes I selected", "current work", or a handoff, call
  `get_active_context` first, then read only relevant Notes with `get_note`.
  An empty Current Context is meaningful; do not silently replace it with a
  library-wide search.
- When the task may depend on older context, call `search_notes` proactively.
  Search returns bounded discovery data; call `get_note` only for relevant hits.
- For long Notes, follow `next_cursor`, use the returned outline, or use a
  focused query. Do not ask the user to paste the entire Note.
- Write only when the user expresses durable save intent or an explicit project
  policy authorizes it. Use `create_note` for a new artifact, `append_note` for
  chronological progress or handoff material, and `edit_note` for a precise
  correction.
- Every create, append, or edit needs a fresh `operation_id` and a short English
  `profile_hint` describing the durable user intent. Reuse an operation ID only
  to retry the exact same request after a lost response.
- Use `manage_collections` only on explicit organization instructions. Never
  infer a Collection, auto-classify Notes, or create one implicitly during a
  move.
- Call `delete_note` only when the user explicitly asks. Never delete as a side
  effect of cleanup, deduplication, or organization.
- Discover attachments from `get_note`, then use `read_attachment`. If a live
  write schema has no file input, attachment writing is unsupported for this
  host; do not invent or expose storage transport steps.
- Ask first before storing sensitive, uncertain, inferred, or surprising
  personal data. Never store secrets, OAuth tokens, passwords, private keys, or
  recovery codes in CA3.

If a tool or action is absent, treat it as ungranted capability. Do not probe
hidden tools by name; explain the missing permission or ask the user to
reauthorize when the requested action requires it.

## Stale Thread Handling

Codex Desktop may keep plugin skill and OAuth connection state per thread. After installing, updating, or re-authenticating CA3, start a new Codex thread before testing.

If a thread reports `oauth_refresh_token_missing` or `TRIGGER_REAUTHENTICATION`, authenticate CA3 again and then open a new thread.

If a thread reports missing or mismatched `SKILL.md` paths, do not debug CA3 by reading local plugin cache paths. Use MCP tool discovery or open a new thread.
