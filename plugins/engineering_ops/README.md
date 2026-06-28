# engineering_ops

The engineering operations toolkit. Reads your teams' Jira, classifies ticket health against a shared standard, and produces reports. The morning report is the first command. More commands (ticket research, weekly rollup) drop in beside it without new standards.

## Contents

* skills/ticket_standards: definitions of blocked, at risk, and stale, thresholds, escalation, and JQL patterns. Loads automatically for any ticket report.
* commands/morning_report: the `/engineering_ops:morning_report` slash command. Orchestration plus the report's output shape.
* .mcp.json and connectors.md: the Atlassian and Slack connectors this plugin needs.

## Depends on

house_standards, for the shared `report_style` skill. Install both. In a Team or Enterprise marketplace, set house_standards to install automatically so it is always present.

## Setup

1. Install house_standards and engineering_ops from the marketplace.
2. Authorize the Atlassian and Slack connectors. See connectors.md.
3. Copy config/config.example.md from the marketplace repo to a `config.md` in your Cowork working folder, and fill it in. Config stays local to you and is never committed.
4. Run `/engineering_ops:morning_report` by hand once. The first run usually reveals that your real Jira status names do not match the config mapping. Fix config and rerun.
5. To run it every morning, schedule a Cowork task that calls the command. See the command file for the scheduling note.

## Adding a command

Drop a new markdown file in commands/. It becomes `/engineering_ops:its_name`. Reuse the ticket_standards and report_style skills rather than writing new definitions, so every engineering report speaks the same language. Bump the plugin version and note it in the repo CHANGELOG.
