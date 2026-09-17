---
name: ainoflow-files
description: File upload, download, and list via Ainoflow Files MCP. Use when working with user files in Ainoflow storage.
---

# Ainoflow Files

Use the **ainoflow-files** MCP server for binary file storage (uploads, downloads, and listings) in Ainoflow.

## Start here

Call this server’s guide tool first (`files_guide` or whichever `*_guide` tool it exposes). Use those tools for upload, download, list, and links rather than pasting file bytes into chat.

## When to use

- Store a user file in Ainoflow instead of only on the local disk
- Retrieve a previously uploaded file or a link to it

## First task

Call `files_guide`, upload a small non-secret file, then list or fetch it and confirm you can retrieve the same object (or a link the server returns).

Do not commit downloaded credentials or embed API keys in stored files.

Auth is the Cursor plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
