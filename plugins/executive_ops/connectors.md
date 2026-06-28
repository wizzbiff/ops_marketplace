# Connectors for executive_ops

This plugin reads from four connectors and writes to none of the data systems.

| Connector | Access | Why |
|-----------|--------|-----|
| Looker | read | runs the configured looks for growth and product metrics |
| NetSuite | read | pulls the configured financial report for the closed month |
| Atlassian (Jira) | read | reads the OKR objectives and key results for the quarter |
| Slack | read and write | posts the summary and board-attention pointer to a private leadership channel |

## How they get connected

The way connectors attach to a Cowork plugin is the part of this setup most likely to differ from what is written here, because plugin support is a research preview and the format moves. Treat the steps below as the intent and verify against the current Cowork plugin docs.

* **Looker and NetSuite** are not part of the default connector set the engineering plugin uses. They must be added in Cowork before the board pack runs, and depending on your workspace they may need a custom or MCP-based connector rather than a managed one. Point the `.mcp.json` in this plugin at the appropriate remote MCP endpoints by replacing `{{LOOKER_MCP_URL}}` and `{{NETSUITE_MCP_URL}}`, or wire them through whatever your org standardizes on. Both are **read only** here: the board pack never writes to a BI or finance system.
* **Atlassian** is read only for this plugin (OKR objectives and key results). Authorize the managed Atlassian connector in the connector directory, or point the `.mcp.json` at Atlassian's official remote MCP endpoint by replacing `{{ATLASSIAN_MCP_URL}}`.
* **Slack** is an Anthropic managed connector. Authorize it once in the connector directory. When you customize this plugin in Cowork, you can bundle it so adopters are prompted to connect.

## A note on sensitivity and reach

The board pack carries financial and OKR data. It posts only to the configured private leadership channel, and is primarily a written artifact a person carries to the board. In Cowork, connectors reach external services through Anthropic's cloud, not your local network, so any custom MCP endpoint for Looker or NetSuite must be reachable over the public internet from Anthropic's IP ranges.
