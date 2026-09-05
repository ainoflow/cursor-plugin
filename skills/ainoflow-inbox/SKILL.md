---
name: ainoflow-inbox
description: Inbound inbox via Ainoflow Inbox MCP. Use when reading inbound inbox or email items, handling attachments, marking items processed, or deleting them after work. This plugin does not send outbound messages.
---

# Ainoflow Inbox

Use the **ainoflow-inbox** MCP server to receive and process inbound items (including email): read them, handle attachments, mark processed, and delete after work.

This plugin does **not** send outbound messages or notifications.

## Start here

Call this server’s guide tool first (`inbox_guide` or whichever `*_guide` tool it exposes). Use only inbound receive/process tools from that guide.

## When to use

- Read an inbound inbox item or email
- Handle attachments on an inbound item
- Mark an item processed after you finish the work
- Delete an item when the user wants it removed

## First task

List or read an inbound item, summarize it for the user, then mark it processed (or delete it if they asked). Do not attempt to send a reply through this plugin.

Treat inbox content as user data. Do not copy secrets from messages into the repo or logs.

Auth is the Cursor plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
