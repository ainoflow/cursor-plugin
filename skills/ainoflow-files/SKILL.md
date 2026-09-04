---
name: ainoflow-files
description: File upload, download, and list via Ainoflow Files MCP. Use when working with user files in Ainoflow storage.
---

# Ainoflow Files

Use the **ainoflow-files** MCP server for binary file storage (uploads, downloads, and listings) in Ainoflow.

## When to use

- Upload or retrieve user files that should live in Ainoflow, not only on the local disk
- List files already stored for the current account
- Hand off documents or attachments between sessions or agents

## How to use

Call the tools exposed by the `ainoflow-files` MCP server. Use those tools for upload, download, and list rather than copying file bytes into chat.

Do not commit downloaded credentials or embed API keys in stored files.

Auth is the plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
