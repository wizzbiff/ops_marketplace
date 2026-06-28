# engineering_ops

The engineering operations toolkit. Reads your teams' Jira, your own inbox and calendar, and your repos, classifies and triages against shared standards, and produces reports and briefs. Four commands ship today, each reusing the plugin's skills rather than restating definitions.

## Contents

Skills, loaded automatically when a command needs them:

* skills/ticket_standards: blocked, at risk, and stale, thresholds, escalation, and JQL patterns. The base for every engineering command.
* skills/personal_triage: what counts as "needs my attention" across email, Slack, Jira, and calendar, plus dedup, ranking, and caps. Reuses ticket_standards for Jira.
* skills/research_standards: what a ready-to-start ticket brief contains and how to gather it, including GitHub matching by key and the fact-versus-inference rule.
* skills/flow_standards: the weekly flow and risk view — week window, throughput, cycle time, aging, persistent blockers, trend, and the weekly escalation lens. Reuses ticket_standards.

Commands:

* commands/morning_report: `/engineering_ops:morning_report`. Morning ticket health report by team, posted to Slack.
* commands/daily_personal_brief: `/engineering_ops:daily_personal_brief`. A private morning digest of your email, Slack, Jira, and calendar, delivered as a Slack DM to self.
* commands/ticket_research: `/engineering_ops:ticket_research`. On demand, takes a ticket and assembles a ready-to-start brief, optionally posting a pointer comment back to the ticket.
* commands/weekly_leadership_rollup: `/engineering_ops:weekly_leadership_rollup`. Weekly flow and risk rollup across teams for leadership, with week-over-week trend, posted to Slack.

* .mcp.json and connectors.md: the connectors these commands need. See connectors.md for which command uses which.

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
