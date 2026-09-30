# Ainoflow Cursor plugin

Phase 1 **Cursor Plugin** that wires four remote [Ainoflow MCP](https://www.ainoflow.io/docs/mcp) services — **Memory**, **Files**, **Storage**, and **Inbox** — for Cursor (and Grok Bot in Cursor).

This repository is a Cursor Plugin root: `.cursor-plugin/plugin.json` + `.cursor-plugin/marketplace.json` + `mcp.json` + `skills/` + `assets/logo.png`.

Agent Plugins portability (stdio transports, portable HTTP header expansion) is **out of scope for v1**. `${AINOFLOW_API_KEY}` is a Cursor Plugin Variable. It is substituted by Cursor (**Plugins → Configure**). Do not expect Bearer placeholders to work in a pure Agent Plugins client.

## Quick start

You need an [Ainoflow](https://www.ainoflow.io) account and an API key from the Ainoflow Dashboard. This repo stays private until an explicit post-merge greenlight. Install locally for now; marketplace publish follows the [Marketplace publish runbook](#marketplace-publish-runbook).

### 1. Install the plugin locally

**Preferred:** clone or copy the plugin root to:

- macOS / Linux: `~/.cursor/plugins/local/ainoflow`
- Windows: `%USERPROFILE%\.cursor\plugins\local\ainoflow`

A symlink at that path works only if it resolves to a directory **inside** `~/.cursor/plugins/local`. Cursor skips a symlink that points to a plugin repository elsewhere on disk — clone or copy into the folder above when in doubt.

**Or** add this repo folder as a marketplace in Cursor. That path uses `.cursor-plugin/marketplace.json` (single-plugin marketplace with `"source": "."`).

### 2. Set `AINOFLOW_API_KEY`

1. Open **Plugins → ainoflow → Configure**.
2. Paste the Dashboard API key only (raw key). Do **not** type a `Bearer ` prefix — that becomes `Bearer Bearer …` and returns HTTP 401.
3. Restart Cursor, or run **Developer: Reload Window**.
4. Confirm four green MCP servers: `ainoflow-memory`, `ainoflow-files`, `ainoflow-storage`, `ainoflow-inbox`.

If MCP Logs show the literal `${AINOFLOW_API_KEY}` instead of a token, local Variables did not substitute. For a local smoke-test only:

1. Set the OS environment variable `AINOFLOW_API_KEY` to the same raw Dashboard key (no `Bearer ` prefix).
2. Add the four servers to `~/.cursor/mcp.json` (Windows: `%USERPROFILE%\.cursor\mcp.json`) using `Authorization: Bearer ${env:AINOFLOW_API_KEY}`:

```json
{
  "mcpServers": {
    "ainoflow-memory": {
      "url": "https://mcp.ainoflow.io/mcp/v1/memory",
      "headers": {
        "Authorization": "Bearer ${env:AINOFLOW_API_KEY}"
      }
    },
    "ainoflow-files": {
      "url": "https://mcp.ainoflow.io/mcp/v1/files",
      "headers": {
        "Authorization": "Bearer ${env:AINOFLOW_API_KEY}"
      }
    },
    "ainoflow-storage": {
      "url": "https://mcp.ainoflow.io/mcp/v1/storage/json",
      "headers": {
        "Authorization": "Bearer ${env:AINOFLOW_API_KEY}"
      }
    },
    "ainoflow-inbox": {
      "url": "https://mcp.ainoflow.io/mcp/v1/inbox",
      "headers": {
        "Authorization": "Bearer ${env:AINOFLOW_API_KEY}"
      }
    }
  }
}
```

The committed plugin `mcp.json` stays `Bearer ${AINOFLOW_API_KEY}`. Do not switch the plugin file to `${env:...}` only. Never commit a filled-in user `mcp.json` or a real key.

If all four `ainoflow-*` servers fail or Cursor shows OAuth, see [Troubleshooting: 401 and Cursor OAuth](#troubleshooting-401-and-cursor-oauth).

### 3. First tasks

After the four servers connect, run one verifiable task per service (no secrets in the content):

1. **Memory** — Call `memory_guide`, write a short decision (what you chose and why), then `memory_search` → `memory_list` → `memory_read` → `memory_edit` → `memory_context` and confirm the same text.
2. **Storage** — Call `storage_guide`, write a small JSON document (for example `{ "status": "ok" }`), and read the same key back.
3. **Files** — Call `files_guide`, upload a small non-secret file, then retrieve it or obtain a link the Files server returns.
4. **Inbox** — Call `inbox_guide`, list or read an inbound email or inbox item, and summarize it. Mark it processed only after completing the requested processing task. A read-only request leaves status unchanged. Delete only when asked. This plugin does not send mail.

## Auth

Phase 1 auth is **Cursor Variables** via **Plugins → Configure**, with an OS env + user `mcp.json` fallback when local Variables stay literal.

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

Marketplace / plugin-variable install substitutes `${AINOFLOW_API_KEY}` from Plugins → Configure. If MCP Logs show the literal `${AINOFLOW_API_KEY}` instead of a token, Variables were not applied (common on some local folder / local marketplace installs). Use the Quick start OS env + user `mcp.json` fallback (`Bearer ${env:AINOFLOW_API_KEY}`). The committed plugin `mcp.json` stays `Bearer ${AINOFLOW_API_KEY}`.

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

## Marketplace publish runbook

Maintainer dogfood path for the Cursor Marketplace. Do not merge until QA **PASS**. Do not flip GitHub visibility or submit until the later gates are green. Visibility change is never automatic.

After listing, users install **ainoflow** from [cursor.com/marketplace](https://cursor.com/marketplace) and set `AINOFLOW_API_KEY` under **Plugins → Configure**.

### 1. QA PASS, then Product Owner merge

- [ ] QA records **PASS** on the local [Quick start](#quick-start) (four green MCP servers + First tasks)
- [ ] Product Owner merges this PR into `main`
- [ ] Repository stays **private** through merge. Do not change GitHub visibility in the merge.

### 2. Pre-public leak audit

Run on merged `main` (and any tag you will publish) **before** anyone changes GitHub visibility:

- [ ] No API keys, tokens, `.env` files, dashboard dumps, or customer data
- [ ] No internal specs, private runbooks, or staff-only docs
- [ ] No links to `ainoflow-core` or other private repositories
- [ ] Docs and skills link only to public MCP docs: [www.ainoflow.io/docs/mcp](https://www.ainoflow.io/docs/mcp)
- [ ] Committed `mcp.json` still uses `Bearer ${AINOFLOW_API_KEY}` only (placeholder, not a real key)
- [ ] Git history on the default branch has the same constraints (no leaked secrets in old commits)

### 3. Flip the GitHub repo to public (explicit greenlight)

The Cursor public marketplace requires a **public** GitHub repository. This is a separate Product Owner / security greenlight — **not** implied by merge.

- [ ] Leak audit PASS
- [ ] Named owner explicitly greenlights making `ainoflow/cursor-plugin` public
- [ ] Change GitHub visibility to public
- [ ] Confirm [github.com/ainoflow/cursor-plugin](https://github.com/ainoflow/cursor-plugin) is reachable without auth

### 4. Submit to Cursor Marketplace

- [ ] Open [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish)
- [ ] Submit repository URL: `https://github.com/ainoflow/cursor-plugin`
- [ ] Wait for Cursor review

### 5. Cursor official submission checklist

From the [Plugins reference](https://cursor.com/docs/reference/plugins) submission checklist:

- [ ] Valid `.cursor-plugin/plugin.json` (`name`: `ainoflow`, lowercase kebab-case, unique)
- [ ] `description` explains Memory, Files, Storage, and Inbox
- [ ] `README.md` documents install, Variables, and configuration
- [ ] Logo committed and referenced (`"logo": "assets/logo.png"`, official mark from https://www.ainoflow.io/logo.png)
- [ ] Every `${VAR}` in `mcp.json` is declared in `plugin.json` `variables` (`AINOFLOW_API_KEY`)
- [ ] All included components have valid files and frontmatter (four `skills/*/SKILL.md`)
- [ ] All manifest paths are relative and valid (no `..`, no absolute paths)
- [ ] Plugin tested locally (Quick start)
- [ ] GitHub repository is **public** (only after step 3)

Also keep for this package:

- [ ] `.cursor-plugin/marketplace.json` present for folder / marketplace install (`source: "."`)
- [ ] `mcp.json` lists four remote servers with `url` + `Authorization: Bearer ${AINOFLOW_API_KEY}`
- [ ] Inbox skill and README do not claim outbound send

## Documentation

Full MCP connection and configuration: [https://www.ainoflow.io/docs/mcp](https://www.ainoflow.io/docs/mcp)

## License

MIT. See [LICENSE](LICENSE).
