---
name: ainoflow-memory
description: Durable agent/user memory via Ainoflow Memory MCP. Use when remembering facts, preferences, or context across sessions.
---

# Ainoflow Memory

Use the **ainoflow-memory** MCP server for durable memory: markdown documents with revisions, wiki-links, and ranked recall.

## Start here

Call this server’s guide tool first (`memory_guide` or whichever `*_guide` tool it exposes). Use that contract — do not invent endpoints or local files for long-lived memory.

## When to use

- Persist facts, preferences, or a project decision the user wants remembered later
- Restore prior notes or decisions in a new Cursor session

## First task

Call `memory_guide`, then write a short decision (what was chosen and why). Find it with `memory_search` and `memory_list`, read it back, edit a small clarification, and confirm it via `memory_context`.

Keep entries concise. Do not store API keys, tokens, or other secrets in memory documents.

Auth is the Cursor plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
