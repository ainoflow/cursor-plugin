---
name: ainoflow-storage
description: JSON key-value and structured storage via Ainoflow Storage MCP. Use when persisting structured app or agent state as JSON.
---

# Ainoflow Storage

Use the **ainoflow-storage** MCP server for JSON key-value storage of structured app or agent state.

## Start here

Call this server’s guide tool first (`storage_guide` or whichever `*_guide` tool it exposes). Store valid JSON; follow the categories and keys the guide describes.

## When to use

- Persist structured state (settings, job records, checkpoints) as JSON
- Read that state back in a later turn or session

## First task

Call `storage_guide`, write a small JSON document (for example `{ "status": "ok" }` under a test key), then read the same key back and confirm the document matches.

Do not write API keys, tokens, or personal secrets into stored JSON.

Auth is the Cursor plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
