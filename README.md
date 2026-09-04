# Ainoflow Cursor plugin

Phase 1 Agent Plugin that wires four remote [Ainoflow MCP](https://www.ainoflow.io/docs/mcp) services — **Memory**, **Files**, **Storage**, and **Inbox** — for Cursor and Grok Bot agents.

This repository is the plugin root (`plugin.json` + `mcp.json` + `skills/`).

## Prerequisites

- An [Ainoflow](https://www.ainoflow.io) account
- An API key from the Ainoflow Dashboard (Bearer token for all MCP endpoints)

## Install from the marketplace

Browse plugins at [cursor.com/marketplace](https://cursor.com/marketplace). After this plugin is published, install **ainoflow** from there and set `AINOFLOW_API_KEY` when Cursor prompts you (Plugins → Configure).

Publish path for maintainers: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

## Local test

1. Clone this repo (or symlink the plugin directory) into `~/.cursor/plugins/local`, or the current Cursor local-plugin path if it differs.
2. Set `AINOFLOW_API_KEY` via **Plugins → Configure** (preferred). Cursor substitutes `${AINOFLOW_API_KEY}` in `mcp.json`. You can also export it in the environment if your Cursor build documents env fallback for plugin variables.
3. Restart Cursor.
4. Confirm the four MCP servers connect: `ainoflow-memory`, `ainoflow-files`, `ainoflow-storage`, `ainoflow-inbox`.

## Auth

All four servers use Streamable HTTP (JSON-RPC 2.0) and the same header:

```http
Authorization: Bearer ${AINOFLOW_API_KEY}
```

- Create the key in the Ainoflow Dashboard.
- Never commit keys, tokens, `.env` files, or dashboard dumps.
- This repo only stores the `${AINOFLOW_API_KEY}` placeholder.

### Cursor variables vs Agent Plugins

- Root `plugin.json` is the **Agent Plugins 1.0.0** source of truth (portable name, version, skills, MCP).
- Root `mcp.json` is the single MCP config (discovered from the plugin root).
- `.cursor-plugin/plugin.json` exists only so the Cursor marketplace can collect `AINOFLOW_API_KEY` (JSON Schema `variables`). It does not duplicate server URLs.

Agent Plugins portable headers do not expand environment placeholders. Cursor marketplace install does, via Variables.

## MCP endpoints

Transport: Streamable HTTP, JSON-RPC 2.0. Docs: [www.ainoflow.io/docs/mcp](https://www.ainoflow.io/docs/mcp).

| Service | MCP server id | Endpoint |
| --- | --- | --- |
| Memory | `ainoflow-memory` | `https://mcp.ainoflow.io/mcp/v1/memory` |
| Files | `ainoflow-files` | `https://mcp.ainoflow.io/mcp/v1/files` |
| Storage | `ainoflow-storage` | `https://mcp.ainoflow.io/mcp/v1/storage/json` |
| Inbox | `ainoflow-inbox` | `https://mcp.ainoflow.io/mcp/v1/inbox` |

## Skills

| Skill | Use when |
| --- | --- |
| `ainoflow-memory` | Remembering facts, preferences, or context across sessions |
| `ainoflow-files` | Uploading, downloading, or listing user files in Ainoflow |
| `ainoflow-storage` | Persisting structured app/agent state as JSON |
| `ainoflow-inbox` | Sending or receiving inbox items or notifications |

Each skill tells the agent to use the matching MCP server's tools. Recipes stay generic; the live tool list comes from the connected server.

## Publish checklist

Before submitting at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish):

- [ ] Root `plugin.json` validates against [Agent Plugins 1.0.0](https://agent-plugins.org/schemas/1.0.0/plugin.schema.json)
- [ ] `mcp.json` validates against [Agent Plugins MCP 1.0.0](https://agent-plugins.org/schemas/1.0.0/mcp.schema.json)
- [ ] Four `streamable-http` servers are present and use `${AINOFLOW_API_KEY}` only
- [ ] `.cursor-plugin/plugin.json` declares the `AINOFLOW_API_KEY` variable
- [ ] Four skills exist under `skills/*/SKILL.md` with name + when-to-use description
- [ ] No API keys, tokens, `.env` files, or customer data in git
- [ ] Local install connects all four MCP servers
- [ ] Repository link and README are ready for marketplace review

Cursor marketplace listing may later require a public GitHub repository. This repo stays private until there is an explicit greenlight to change visibility. Visibility is not part of the Phase 1 scaffold.

## Documentation

Full MCP connection and configuration: [https://www.ainoflow.io/docs/mcp](https://www.ainoflow.io/docs/mcp)

## License

MIT. See [LICENSE](LICENSE).
