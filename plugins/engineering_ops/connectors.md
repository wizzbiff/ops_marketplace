# Connectors for engineering_ops

This plugin needs two connectors.

| Connector | Access | Why |
|-----------|--------|-----|
| Atlassian (Jira) | read and write | queries tickets, reads links and flags |
| Slack | read and write | posts the report to a channel |

## How they get connected

The way connectors attach to a Cowork plugin is the part of this setup most likely to differ from what is written here, because plugin support is a research preview and the format moves. Treat the steps below as the intent and verify against the current Cowork plugin docs.

* **Slack** is an Anthropic managed connector. Authorize it once in the Cowork connector directory. When you customize this plugin in Cowork, you can bundle it so adopters are prompted to connect rather than wiring it themselves.
* **Atlassian** can be attached two ways. Either authorize the managed Atlassian connector in the connector directory, or point the `.mcp.json` in this plugin at Atlassian's official remote MCP endpoint by replacing `{{ATLASSIAN_MCP_URL}}`. Use whichever your org already standardizes on.

## A note on reach

In Cowork, connectors reach external services through Anthropic's cloud, not your local network. Any custom MCP endpoint must be reachable over the public internet from Anthropic's IP ranges. Managed connectors like Slack and Atlassian already satisfy this.
