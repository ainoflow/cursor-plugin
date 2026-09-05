# Ainoflow Cursor plugin

Phase 1 **Cursor Plugin** that wires four remote [Ainoflow MCP](https://www.ainoflow.io/docs/mcp) services — **Memory**, **Files**, **Storage**, and **Inbox** — for Cursor (and Grok Bot in Cursor).

This repository is a Cursor Plugin root: `.cursor-plugin/plugin.json` + `.cursor-plugin/marketplace.json` + `mcp.json` + `skills/` + `assets/logo.png`.

Agent Plugins portability (stdio transports, portable HTTP header expansion) is **out of scope for v1**. `${AINOFLOW_API_KEY}` is a Cursor Plugin Variable. It is substituted by Cursor (Plugins → Configure). Do not expect Bearer placeholders to work in a pure Agent Plugins client.

## Prerequisites

- An [Ainoflow](https://www.ainoflow.io) account
- An API key from the Ainoflow Dashboard (Bearer token for all MCP endpoints)
- Cursor with Plugins / Variables support

## Install from the marketplace

Browse plugins at [cursor.com/marketplace](https://cursor.com/marketplace). After this plugin is published, install **ainoflow** and set `AINOFLOW_API_KEY` when Cursor prompts you (**Plugins → Configure** / Variables).

Publish path for maintainers: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

## Local test

Two install paths:

- **Preferred (single plugin):** clone or symlink the plugin root to `~/.cursor/plugins/local/ainoflow`.
- **Add folder as a marketplace:** choose this repo folder in Cursor. That path requires `.cursor-plugin/marketplace.json` (this repo includes a single-plugin marketplace with `source: "."`).

Then:

1. Set `AINOFLOW_API_KEY` via **Plugins → Configure**. Cursor substitutes `${AINOFLOW_API_KEY}` in `mcp.json`.
2. Restart Cursor.
3. Confirm the four MCP servers connect: `ainoflow-memory`, `ainoflow-files`, `ainoflow-storage`, `ainoflow-inbox`.

If all four `ainoflow-*` servers fail or Cursor shows OAuth, see [Troubleshooting: 401 and Cursor OAuth](#troubleshooting-401-and-cursor-oauth). The plugin endpoints and `mcp.json` format are correct; a rejected or empty API key is the usual cause.

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

### Troubleshooting: 401 and Cursor OAuth

Ainoflow MCP is **Bearer API key only**. The four plugin endpoints and the committed `mcp.json` (`Authorization: Bearer ${AINOFLOW_API_KEY}`) are correct. Breakage is: bad or empty key → HTTP 401 with an empty body and **no** `WWW-Authenticate` → Cursor already sent `Authorization`, OAuth fallback fails → all four `ainoflow-*` servers are marked failed, so tools never appear (no `memory_guide` or other tools). This is not Memory-specific.

Cursor **Output → MCP Logs** typically shows:

- `OAuth fallback failed after MCP server returned 401 for configured Authorization header`
- `HTTP 401 Unauthorized … while using configured Authorization header`

The plugin sends the Dashboard API key from **Plugins → Configure**, not OAuth. Cursor’s `mcp_auth` / OAuth UI is useless here — Ainoflow does not offer OAuth for Memory, Files, Storage, or Inbox. Do **not** add an OAuth `auth` block to this plugin’s `mcp.json`.

**Fix a first install (empty or wrong key)**

1. Open **Plugins → ainoflow → Configure**.
2. Paste the Dashboard API key only. Do **not** type `Bearer ` in front of it (that becomes `Bearer Bearer …` and 401s).
3. Reload MCP servers or restart Cursor.
4. Expect four green `ainoflow-*` entries in **Settings → MCP**.

**If the key is set and you still get 401**

The key was rejected (wrong value, leading/trailing whitespace, a `Bearer ` prefix, or not an MCP API key). Confirm outside Cursor with a JSON-RPC `initialize` (replace `YOUR_KEY`; never commit a real key):

```bash
curl -sS -X POST https://mcp.ainoflow.io/mcp/v1/memory \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"ainoflow-check","version":"0.1.0"}}}'
```

Expect a JSON-RPC `initialize` result, not HTTP 401.

**Placeholders vs Variables**

Marketplace install substitutes `${AINOFLOW_API_KEY}` from Plugins → Configure. If MCP Logs show the literal `${AINOFLOW_API_KEY}` instead of a token, Variables were not applied (common on some local folder / local marketplace installs). For a local smoke-test only, set the OS env `AINOFLOW_API_KEY` and put the four URLs in `~/.cursor/mcp.json` (Windows: `%USERPROFILE%\.cursor\mcp.json`) with `Authorization: Bearer ${env:AINOFLOW_API_KEY}`. The committed plugin `mcp.json` stays `Bearer ${AINOFLOW_API_KEY}` — do not switch it to `${env:...}` only.

Never commit real keys, tokens, or a filled-in `mcp.json`.

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

- [ ] `.cursor-plugin/plugin.json` is the primary plugin manifest (`name`: `ainoflow`)
- [ ] `.cursor-plugin/marketplace.json` is present for folder / marketplace install (`source: "."`)
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
