# Ainoflow Cursor plugin

Phase 1 **Cursor Plugin** that wires four remote [Ainoflow MCP](https://www.ainoflow.io/docs/mcp) services — **Memory**, **Files**, **Storage**, and **Inbox** — for Cursor (and Grok Bot in Cursor).

This repository is a Cursor Plugin root: `.cursor-plugin/plugin.json` + `mcp.json` + `skills/` + `assets/logo.png`.

Agent Plugins portability (stdio transports, portable HTTP header expansion) is **out of scope for v1**. `${AINOFLOW_API_KEY}` is a Cursor Plugin Variable. It is substituted by Cursor (Plugins → Configure). Do not expect Bearer placeholders to work in a pure Agent Plugins client.

## Prerequisites

- An [Ainoflow](https://www.ainoflow.io) account
- An API key from the Ainoflow Dashboard (Bearer token for all MCP endpoints)
- Cursor with Plugins / Variables support

## Install from the marketplace

Browse plugins at [cursor.com/marketplace](https://cursor.com/marketplace). After this plugin is published, install **ainoflow** and set `AINOFLOW_API_KEY` when Cursor prompts you (**Plugins → Configure** / Variables).

Publish path for maintainers: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

## Local test

1. Clone this repo (or symlink the plugin root) into `~/.cursor/plugins/local/ainoflow`.
2. Set `AINOFLOW_API_KEY` via **Plugins → Configure**. Cursor substitutes `${AINOFLOW_API_KEY}` in `mcp.json`.
3. Restart Cursor.
4. Confirm the four MCP servers connect: `ainoflow-memory`, `ainoflow-files`, `ainoflow-storage`, `ainoflow-inbox`.

## Auth

Phase 1 auth is **Cursor Variables only**.

All four servers use remote Streamable HTTP (JSON-RPC 2.0) and the same header, with the Cursor variable placeholder:

```http
Authorization: Bearer ${AINOFLOW_API_KEY}
```

- Create the key in the Ainoflow Dashboard.
- Set it in Cursor under **Plugins → Configure**. The plugin never stores the secret.
- Never commit keys, tokens, `.env` files, or dashboard dumps.
- This repo only stores the `${AINOFLOW_API_KEY}` placeholder, declared in `.cursor-plugin/plugin.json` `variables`.

There is no root Agent Plugins `plugin.json` in v1, so this package is not advertised as a portable Agent Plugins auth setup.

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
| `ainoflow-inbox` | Reading inbound inbox/email, handling attachments, marking processed, or deleting after work (no send) |

Each skill starts with that server’s guide tool. Recipes stay generic; the live tool list comes from the connected server.

## First tasks

After the four servers connect, try one verifiable task per service (no secrets in the content):

1. **Memory** — Save a short decision (what you chose and why). In a new chat/session, restore that decision from Memory and confirm the text matches.
2. **Storage** — Write a small JSON document (for example `{ "status": "ok" }`) and read the same key back.
3. **Files** — Upload a small non-secret file, then retrieve it or obtain a link the Files server returns.
4. **Inbox** — List or read an inbound email or inbox item and summarize it. Mark it processed only after completing the requested processing task. A read-only request leaves status unchanged. Delete only when asked. This plugin does not send mail.

## Publish checklist

Before submitting at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish):

- [ ] `.cursor-plugin/plugin.json` is the primary manifest (`name`: `ainoflow`)
- [ ] `variables` declares required `AINOFLOW_API_KEY`; every `${VAR}` in `mcp.json` is in that schema
- [ ] `"logo": "assets/logo.png"` is set (official mark from https://www.ainoflow.io/logo.png)
- [ ] `mcp.json` lists four remote servers with `url` + `Authorization: Bearer ${AINOFLOW_API_KEY}`
- [ ] Four skills exist under `skills/*/SKILL.md` with name + when-to-use description
- [ ] Inbox skill and README do not claim outbound send
- [ ] No API keys, tokens, `.env` files, or customer data in git
- [ ] Local install connects all four MCP servers
- [ ] Repository link and README are ready for marketplace review

The public Cursor marketplace requires a **public** GitHub repository (Cursor marketplace docs). This repo stays private until an explicit greenlight to flip visibility as part of the publish checklist. Visibility change is not part of this PR’s merge.

## Documentation

Full MCP connection and configuration: [https://www.ainoflow.io/docs/mcp](https://www.ainoflow.io/docs/mcp)

## License

MIT. See [LICENSE](LICENSE).
