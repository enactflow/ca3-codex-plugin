# CA3 Codex Plugin

<img src="assets/ca3-icon-rounded.png" alt="CA3" width="96">

CA3 gives AI agents a private, user-owned memory layer so important context can follow you across ChatGPT, Codex, Claude Code, and browser workflows.

This repository is a Codex plugin marketplace source for connecting Codex to the public CA3 OAuth MCP endpoint.

## Install

Add the CA3 marketplace, then install the `ca3` plugin:

```bash
codex plugin marketplace add enactflow/ca3-codex-plugin
codex plugin add ca3@ca3
```

On first use, Codex should open the CA3 OAuth flow and ask you to authorize the requested scopes.

Start a new Codex thread after installing so the CA3 skill and MCP server are loaded cleanly.

## Upgrade

Refresh the CA3 marketplace snapshot, then reinstall the plugin from that snapshot:

```bash
codex plugin marketplace upgrade ca3
codex plugin remove ca3@ca3
codex plugin add ca3@ca3
```

Start a new Codex thread after upgrading. Existing threads may keep an older plugin skill or OAuth connection snapshot.

## Verify

Confirm CA3 is installed and enabled:

```bash
codex plugin list --json
```

Look for `ca3@ca3` with version `0.2.0` or newer and `"enabled": true`.

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

CA3 behavior is defined by the scope-shaped live MCP tool descriptions exposed by:

```text
https://ca3.dribwise.ai/mcp
```

The plugin skill supplies trigger and workflow guidance without duplicating the
live schemas. The logical surface lets Codex continue from the user's Current
Context, discover prior Notes, read long Notes in bounded chunks, and safely
create, append, precisely edit, organize, explicitly delete, or read attached
material. The exact tools shown depend on the scopes the user granted.

For content writes, choose the smallest semantic operation:

- `create_note` for a new standalone artifact.
- `append_note` for chronological progress or handoff material.
- `edit_note` for exact, local corrections to an existing Note.

All three require a fresh `operation_id` and a short `profile_hint` describing
the durable user intent. Reuse the same operation ID only when retrying the same
request after a lost response.

Attachments are discovered through `get_note` and read through
`read_attachment`. Low-level upload sessions and signed policy mechanics are not
model-facing tools. If the live Note write schema does not expose a host file
input, report attachment writing as unsupported instead of inventing a transport
flow.

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
