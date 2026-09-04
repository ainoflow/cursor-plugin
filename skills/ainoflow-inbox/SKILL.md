---
name: ainoflow-inbox
description: Inbox messaging via Ainoflow Inbox MCP. Use when sending or receiving inbox items or notifications through Ainoflow.
---

# Ainoflow Inbox

Use the **ainoflow-inbox** MCP server for inbox messaging: receiving and processing inbox items (including email) and related notifications.

## When to use

- Check or process inbound inbox items
- Send or record inbox messages / notifications through Ainoflow
- Connect agent work to the user's Ainoflow inbox

## How to use

Call the tools exposed by the `ainoflow-inbox` MCP server. Treat inbox content as user data: summarize when possible, and do not copy secrets from messages into the repo or logs.

Auth is the plugin variable `${AINOFLOW_API_KEY}` (Ainoflow Dashboard). See [MCP Protocol](https://www.ainoflow.io/docs/mcp).
