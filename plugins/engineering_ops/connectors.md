# Connectors for engineering_ops

This plugin's commands use the connectors below. A given command uses only the ones it needs; you only have to authorize the connectors for the commands you run.

| Connector | Access | Used by | Why |
|-----------|--------|---------|-----|
| Atlassian (Jira) | read and write | morning_report, daily_personal_brief, ticket_research, weekly_leadership_rollup | queries tickets, reads links, flags, and status history. Write is used only by ticket_research, to post one pointer comment on the input ticket; every other command reads only. |
| Atlassian (Confluence) | read | ticket_research | pulls linked docs for context |
| Slack | read and write | morning_report, daily_personal_brief, ticket_research, weekly_leadership_rollup | posts reports to a channel, sends the personal brief as a DM to self; ticket_research reads threads only |
| Gmail | read | daily_personal_brief | reads mail when mail_source is gmail |
| Microsoft 365 | read | daily_personal_brief | reads Outlook mail and calendar when mail_source or calendar_source is m365. Read only: it cannot send mail. |
| Google Calendar | read | daily_personal_brief | reads today's events when calendar_source is google |
| GitHub | read | ticket_research | finds related pull requests, branches, and commits by ticket key, within the configured allowlist |

## How they get connected

The way connectors attach to a Cowork plugin is the part of this setup most likely to differ from what is written here, because plugin support is a research preview and the format moves. Treat the notes below as the intent and verify against the current Cowork plugin docs.

* **Slack, Gmail, Microsoft 365, Google Calendar, and GitHub** are Anthropic managed connectors. Authorize each once in the Cowork connector directory. When you customize this plugin in Cowork, you can bundle the ones a command needs so adopters are prompted to connect rather than wiring them by hand.
* **Atlassian** (Jira and Confluence) can be attached two ways. Either authorize the managed Atlassian connector in the connector directory, or point the `.mcp.json` in this plugin at Atlassian's official remote MCP endpoint by replacing `{{ATLASSIAN_MCP_URL}}`. Use whichever your org already standardizes on.

## Scope notes

* The Atlassian **write** scope is exercised only by `ticket_research`, and only to post or update one pointer comment on the ticket you research. Every other command reads from Atlassian and never writes. If you do not run ticket_research, read alone is enough.
* `daily_personal_brief` branches on config: it uses only the mail and calendar connectors you select (`gmail` or `m365` for mail, `google` or `m365` for calendar). A connector you did not select can be left unauthorized.
* The Microsoft 365 connector is read only. Distribution that needs to reach inboxes goes through Slack; M365 cannot send mail.

## A note on reach

In Cowork, connectors reach external services through Anthropic's cloud, not your local network. Any custom MCP endpoint must be reachable over the public internet from Anthropic's IP ranges. Managed connectors like Slack and Atlassian already satisfy this.
