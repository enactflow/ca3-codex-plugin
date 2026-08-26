# CA3 Codex Plugin

<img src="assets/ca3-icon-rounded.png" alt="CA3" width="96">

CA3 gives AI agents a private, user-owned memory layer so important context can follow you across ChatGPT, Codex, Claude Code, and browser workflows.

This repository is a Codex plugin marketplace source for connecting Codex to the public CA3 OAuth MCP endpoint.

## Install

Add the CA3 marketplace, then install the `ca3` plugin:

```bash
codex plugin marketplace add enactflow/ca3-codex-plugin
codex plugin add ca3@dribwise
```

On first use, Codex should open the CA3 OAuth flow and ask you to authorize the requested scopes.

Start a new Codex thread after installing so the CA3 skill and MCP server are loaded cleanly.

## Upgrade

Refresh the CA3 marketplace snapshot, then reinstall the plugin from that snapshot:

```bash
codex plugin marketplace upgrade dribwise
codex plugin remove ca3@dribwise
codex plugin add ca3@dribwise
```

Start a new Codex thread after upgrading. Existing threads may keep an older plugin skill or OAuth connection snapshot.

### Migrate from `ca3@ca3`

Versions before `0.4.1` used the same `ca3` identifier for both the marketplace and plugin. Migrate that installation once before using later versions:

```bash
codex plugin remove ca3@ca3
codex plugin marketplace remove ca3
codex plugin marketplace add enactflow/ca3-codex-plugin
codex plugin add ca3@dribwise
```

Start a new Codex thread after migration. Do not keep `ca3@ca3` and `ca3@dribwise` enabled together.

## Verify

Confirm CA3 is installed and enabled:

```bash
codex plugin list --json
```

Look for `ca3@dribwise` with version `0.4.4` or newer and `"enabled": true`.

## MCP Endpoint

```text
https://ca3.dribwise.ai/mcp
```

## Plugin Layout

```text
.agents/plugins/marketplace.json
plugins/ca3/.codex-plugin/plugin.json
plugins/ca3/.mcp.json
plugins/ca3/skills/ca3/SKILL.md
```

## Usage

Use CA3 explicitly:

```text
@CA3 remember this project decision.
```

Or let Codex use CA3 automatically when project instructions say CA3 is the shared context surface.

CA3 behavior is defined by the live MCP tool descriptions exposed by:

```text
https://ca3.dribwise.ai/mcp
```

The plugin skill supplies trigger and workflow guidance without duplicating the
live schemas. The stable logical surface lets Codex catch up on current work,
recall prior material across CA3 memory surfaces, preserve durable checkpoints,
maintain confirmed personalization, and precisely work with Notes, Collections,
deletion, and attachments. Granted scopes control execution rather than hiding
tools from discovery.

For foundational context operations:

- `catch_up` for cross-thread continuation and handoff.
- `recall` when earlier decisions, material, progress, or preferences may matter.
- `remember` for a durable cross-thread checkpoint, not ordinary chat noise.
- `update_personalization` for stable, confirmed long-term facts or preferences.

When Codex already knows the task focus, project, host, working directory, or
thread, it supplies that optional context to `catch_up` and `remember`. Missing
metadata is normal and must not trigger a user question. Each `remember` call
creates one atomic Memory event, while reads remain cross-client by default and
preserve the event's authenticated origin.

New connections request CA3's current eleven-scope default grant, including
`attachments:write`. Existing OAuth
grants are not expanded by a plugin upgrade. If a new context tool returns
`insufficient_scope`, reauthorize CA3 and retry in a new thread.

For content writes, choose the smallest semantic operation:

- `create_note` for a new standalone artifact.
- `append_note` for chronological progress or handoff material.
- `edit_note` for exact, local corrections to an existing Note.

All three require a fresh `operation_id` and a short `profile_hint` describing
the durable user intent. Reuse the same operation ID only when retrying the same
request after a lost response.

Attachments are discovered through `get_note` and read through
`read_attachment`. To add a user-requested file, Codex calls
`prepare_attachment`, uploads exact local bytes only when the returned policy
requires it, then passes the prepared `upload_id` to the same atomic
`create_note`, `append_note`, or `edit_note` call. Signed policy details remain
short-lived and must not be printed or persisted. Inline images use complete
Markdown image syntax such as `![Diagram]({{image:diagram}})`; a bare
`{{image:diagram}}` is invalid. Standalone images can add
`{ca3-layout=1 ca3-align=center ca3-width=60}` with `left`, `center`, or `right`
alignment and `auto` or `10..100` width.

## Troubleshooting

After installing, upgrading, or re-authenticating the plugin, start a new Codex thread before testing CA3.

If an old thread reports:

```text
oauth_refresh_token_missing
TRIGGER_REAUTHENTICATION
```

authenticate CA3 again, confirm `codex mcp list` shows `ca3` as enabled with OAuth, then open a new Codex thread.

If an old thread reports that it cannot find `SKILL.md` or that plugin skill paths do not match, treat it as stale Codex plugin cache state. The CA3 MCP tools may still work through tool discovery, but the stable fix is to open a new thread after installation or update.

## License

MIT
