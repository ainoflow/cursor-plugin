---
name: ainoflow-storage
description: JSON key-value and structured storage via Ainoflow Storage MCP. Use when persisting structured app or agent state as JSON.
---

# Ainoflow Storage

Use the **ainoflow-storage** MCP server for JSON key-value storage of structured app or agent state.

## When to use

- Persist structured state (settings, job records, checkpoints) as JSON
- Read, update, or delete named keys in a category/namespace
- Share machine-readable state across sessions

## How to use

Call the tools exposed by the `ainoflow-storage` MCP server. Store valid JSON documents; use categories and keys the product already uses when they exist.

Do not write API keys, tokens, or personal secrets into stored JSON.

Auth is the plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
