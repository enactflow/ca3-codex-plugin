---
name: ca3
description: Bootstrap CA3 usage in Codex. Use the live CA3 MCP tool list and tool descriptions as the source of truth for notes, memory, and personalization behavior.
---

# CA3

CA3 is a user-owned memory hub shared by Codex, ChatGPT, Claude Code, and other MCP clients.

Use CA3 when the user asks to remember, save, recall, search, or update persistent context, or when project instructions say CA3 is the shared memory surface.

## Source Of Truth

Do not infer detailed CA3 behavior from this file. The live MCP tool list and each MCP tool description are the source of truth for:

- Available tools.
- Required arguments.
- Notes, memory, and personalization boundaries.
- Read, write, update, and delete behavior.
- Scope and authorization failures.

If you need to know what CA3 can do, inspect the CA3 MCP tools exposed by the plugin instead of reading local plugin files or guessing from cached state.

## Bootstrap Behavior

- Prefer CA3 MCP tools over local filesystem searches for CA3 memory.
- Search before answering when the request depends on prior saved context.
- Write to CA3 when the user explicitly asks to remember or save durable context.
- Ask first before storing sensitive, uncertain, inferred, or surprising personalization data.
- Never store secrets, OAuth tokens, passwords, private keys, or recovery codes in CA3.

## Stale Thread Handling

Codex Desktop may keep plugin skill and OAuth connection state per thread. After installing, updating, or re-authenticating CA3, start a new Codex thread before testing.

If a thread reports `oauth_refresh_token_missing` or `TRIGGER_REAUTHENTICATION`, authenticate CA3 again and then open a new thread.

If a thread reports missing or mismatched `SKILL.md` paths, do not debug CA3 by reading local plugin cache paths. Use MCP tool discovery or open a new thread.
