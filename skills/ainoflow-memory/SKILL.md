---
name: ainoflow-memory
description: Durable agent/user memory via Ainoflow Memory MCP. Use when remembering facts, preferences, or context across sessions.
---

# Ainoflow Memory

Use the **ainoflow-memory** MCP server for durable memory: markdown documents with revisions, wiki-links, and ranked recall.

## When to use

- Persist facts, preferences, or project context the user wants remembered later
- Recall prior notes or decisions across Cursor / Grok Bot sessions
- Maintain a lightweight wiki of agent-relevant documents

## How to use

Call the tools exposed by the `ainoflow-memory` MCP server. Prefer the server's own guide/list/read/write/search tools over inventing local files for long-lived memory.

Keep entries concise and attributable. Do not store API keys, tokens, or other secrets in memory documents.

Auth is the plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
