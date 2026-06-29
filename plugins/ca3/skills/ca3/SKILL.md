---
name: ca3
description: Use CA3 as the shared AI memory hub for notes, daily memory, and personalization. Trigger when users ask to remember, save, recall, search, update preferences, or preserve durable project context across AI clients.
---

# CA3

CA3 is a user-owned memory hub shared by Codex, ChatGPT, Claude Code, and other MCP clients.

Use CA3 when the user asks to remember, save, recall, search, or update persistent context, or when project instructions say CA3 is the appropriate memory surface.

## Memory Surfaces

- Use `notes` for discrete artifacts: snippets, prompts, references, project notes, decisions, links, and standalone records.
- Use `memory` for date-based ongoing context: daily work logs, travel notes, operational state, recurring project updates, and "today" records.
- Use `personalization` for stable long-term user preferences, durable facts, recurring style choices, and reusable instructions.

## Read Behavior

Search CA3 before answering when the request depends on prior user context, project history, saved decisions, reusable prompts, or preferences likely to exist in CA3.

Prefer scoped reads:

- Search notes for project references, saved snippets, and named artifacts.
- Search memory for recent timeline context and daily updates.
- Read personalization when the answer may depend on stable user preferences.

Do not claim CA3 has no relevant context unless you actually searched the appropriate surface.

## Write Behavior

Write to CA3 when the user explicitly asks to remember or save something.

You may also write without extra confirmation when all of these are true:

- The information is durable and useful beyond the current turn.
- The content is not sensitive credential material.
- The write target is obvious from the content.
- The user or repo instructions indicate CA3 should be used as shared memory.

Ask first when the content is sensitive, uncertain, inferred rather than stated, or could affect future personalization in a surprising way.

## Update Behavior

Prefer updating an existing relevant CA3 item over creating duplicates when the user is revising an existing note, daily memory entry, or personalization profile.

When updating, preserve useful prior context and avoid overwriting user-authored content unless the user clearly asked for replacement.

## Boundaries

Do not store secrets, OAuth tokens, passwords, private keys, or recovery codes in CA3.

Do not use memory or personalization as hidden scratch space. Only store context that the user would reasonably expect to persist.

When unsure whether something belongs in notes, memory, or personalization, ask a short clarification or choose notes as the least invasive default.
