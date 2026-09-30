---
name: ainoflow-inbox
description: Inbound inbox via Ainoflow Inbox MCP. Use when reading inbound inbox or email items, handling attachments, marking items processed, or deleting them after work. This plugin does not send outbound messages.
---

# Ainoflow Inbox

Use the **ainoflow-inbox** MCP server to receive and process inbound items (including email): read them, handle attachments, mark processed after requested work, and delete only when asked. Reading does not change status.

This plugin does **not** send outbound messages or notifications.

## Start here

Call this server’s guide tool first (`inbox_guide` or whichever `*_guide` tool it exposes). Use only inbound tools from that guide. A list or read does not mark an item processed.

## When to use

- Read an inbound inbox item or email (read-only; status unchanged)
- Handle attachments on an inbound item
- Mark an item processed only after completing the requested processing task
- Delete an item only when the user asks

## First task

Call `inbox_guide`, then list or read an inbound item and summarize it for the user.
Mark it processed only after completing the requested processing task.
A read-only request leaves its status unchanged.
Delete only when the user asks.

Treat inbox content as user data. Do not copy secrets from messages into the repo or logs.

Auth is the Cursor plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
