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
- Stable tool discovery, execution-time scopes, and resource boundaries.
- Notes, Current Context, Memory, Personalization, Collections, and attachments behavior.
- Read, create, append, exact-edit, organize, and delete behavior.
- Scope and authorization failures.

If you need to know what CA3 can do, inspect the CA3 MCP tools exposed by the plugin instead of reading local plugin files or guessing from cached state.

## Agent Policy

Use proactive read, intent-bound write, and explicit delete:

- For cross-thread continuation, a handoff, or work that clearly depends on the
  user's current state, call `catch_up` proactively. Treat its profile, Current
  Context, and focused or recent Memory events as bounded source material, not
  an invented answer.
- When calling `catch_up`, provide a short task focus when it is already clear.
  Include host, working-directory, or thread environment only when Codex already
  exposes it. Omit unknown values and never interrupt the user to obtain
  provenance metadata. Memory retrieval remains cross-client by default.
- When the user specifically refers to selected Notes, use
  `get_active_context`, then read only relevant Notes with `get_note`. An empty
  Current Context is meaningful; do not silently replace it with a library-wide
  search.
- When past decisions, saved material, progress, or durable preferences may
  affect the task, call `recall` proactively. Keep its Notes, Memory, and
  Personalization results distinct. Use `search_notes` for Note-specific
  discovery and `get_note` when complete Note content is needed.
- For long Notes, follow `next_cursor`, use the returned outline, or use a
  focused query. Do not ask the user to paste the entire Note.
- Write only when the user expresses durable save intent or an explicit project
  policy authorizes it. Use `create_note` for a standalone artifact,
  `append_note` for chronological material that belongs to an existing Note,
  and `edit_note` for a precise correction.
- Every create, append, or edit needs a fresh `operation_id` and a short English
  `profile_hint` describing the durable user intent. Reuse an operation ID only
  to retry the exact same request after a lost response.
- Use `remember` for a high-confidence cross-thread checkpoint such as a durable
  decision, progress state, blocker, or next step. Each successful call creates
  one atomic Memory event. Add the known project hint and available environment
  without asking the user; omission is normal for Web or project-less work.
  Never invent or override the authenticated source client. Do not use Memory
  for ordinary chat noise or as a substitute for a user-managed Note. Each call
  needs a fresh `operation_id` and a short English `profile_hint`.
- Use `update_personalization` only for stable, long-term, reusable user facts or
  preferences. Read `catch_up` first, preserve still-valid profile content, and
  submit a full replacement with its current `expected_revision_id` and a fresh
  `operation_id`. Never auto-merge a conflict. Ask before writing sensitive,
  uncertain, inferred, or surprising information.
- Use `manage_collections` only on explicit organization instructions. Never
  infer a Collection, auto-classify Notes, or create one implicitly during a
  move.
- Call `delete_note` only when the user explicitly asks. Never delete as a side
  effect of cleanup, deduplication, or organization. Deletion is two-phase:
  prepare first, then wait for the user to confirm in the CA3 MCP App. If the
  host has no MCP Apps support, wait for a new explicit text confirmation
  before submitting the returned short-lived confirmation token.
- Discover attachments from `get_note`, then use `read_attachment`. If a live
  write schema has no file input, attachment writing is unsupported for this
  host; do not invent or expose storage transport steps.
- Ask first before storing sensitive, uncertain, inferred, or surprising
  personal data. Never store secrets, OAuth tokens, passwords, private keys, or
  recovery codes in CA3.

The authenticated model-facing CA3 surface is stable across grants. Scopes are
checked when a tool runs, not by hiding tools from discovery. A tool-level
`insufficient_scope` result means the current connection needs incremental
authorization; ask the user to reauthorize and retry. Plugin upgrades do not
silently expand an existing OAuth grant. Do not report a scope failure as
`Tool not found`. If one of the documented tools is genuinely absent, refresh
or reconnect the plugin and start a new thread before treating the server
capability as unavailable.

## Stale Thread Handling

Codex Desktop may keep plugin skill and OAuth connection state per thread. After installing, updating, or re-authenticating CA3, start a new Codex thread before testing.

If a thread reports `oauth_refresh_token_missing` or `TRIGGER_REAUTHENTICATION`, authenticate CA3 again and then open a new thread.

If a thread reports missing or mismatched `SKILL.md` paths, do not debug CA3 by reading local plugin cache paths. Use MCP tool discovery or open a new thread.
