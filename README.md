# CA3 Codex Plugin

CA3 is a user-owned AI memory hub for notes, daily memory book entries, and personalization.

This repository is a Codex plugin marketplace source for connecting Codex to the public CA3 OAuth MCP endpoint.

## Install

Add this marketplace source:

```bash
codex plugin marketplace add enactflow/ca3-codex-plugin
```

Then install `CA3` from the Codex plugin directory.

On first use, Codex should open the CA3 OAuth flow and ask you to authorize the requested scopes.

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

Or let Codex use CA3 automatically when project instructions say CA3 is the shared memory surface.

## License

MIT
