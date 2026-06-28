# Changelog

Versioned history of the marketplace and its plugins. Local config changes by individuals are not tracked here, only changes to plugins and shared structure.

## marketplace 2.0.0

Brought the marketplace to parity with the ops_automations library: the three engineering tasks added since the initial conversion, plus the new executive domain. Major version because a new plugin joined the marketplace.

* engineering_ops 2.0.0: added three skills and three commands, each reusing the existing ticket_standards and report_style skills.
  * skills/personal_triage: what counts as "needs my attention" across email, Slack, Jira, and calendar, with dedup, deterministic ranking, and item caps. Reuses ticket_standards for the Jira buckets.
  * skills/research_standards: what a ready-to-start ticket brief contains and how to gather it, including one-hop link following, GitHub matching by ticket key with the evidence rule, and fact-versus-inference synthesis.
  * skills/flow_standards: the weekly flow and risk view — trailing business week, throughput, cycle time, aging, persistent blockers, week-over-week trend, and the weekly escalation lens. Reuses ticket_standards.
  * commands/daily_personal_brief: private morning digest of email, Slack, Jira, and calendar, delivered as a Slack DM to self. Mail (Gmail or M365) and calendar (Google or M365) sources are config-selectable.
  * commands/ticket_research: on-demand, takes a ticket key or URL, assembles a ready-to-start brief, and optionally posts one pointer comment back to the ticket. The only command that writes to Jira.
  * commands/weekly_leadership_rollup: weekly flow and risk rollup across teams, with trend derived from the previous run's saved output, posted to a leadership channel.
  * connectors: engineering_ops now declares Gmail, Microsoft 365, Google Calendar, and GitHub alongside Atlassian and Slack. Each command uses only the connectors it needs; the Atlassian write scope is exercised only by ticket_research.
* executive_ops 1.0.0: new domain plugin, copied from the template.
  * skills/board_metrics: the executive domain standard. Metric catalog (financial from NetSuite, growth and product from Looker), key-result health by pace to target (achieved / on track / at risk / off track / not started), month-over-month trend, the needs-board-attention lens, and the source-query patterns. Reuses report_style.
  * commands/board_pack: the monthly board metrics pack. Reads Looker, NetSuite, and Jira, computes metrics, key-result health, and trend, writes the pack to output, and posts the summary and board-attention pointer to a private leadership channel. Looker and NetSuite are not in the default connector set and may need a custom or MCP-based connector.
* config/config.example.md: extended with per-command sections for the personal brief, ticket research, the weekly rollup overrides, and the board pack. The board pack section is self-contained and does not depend on the engineering teams or status mapping.
* marketplace.json: registered executive_ops and bumped engineering_ops to 2.0.0 and the marketplace to 2.0.0.

## marketplace 1.0.0

Initial marketplace.

* house_standards 1.0.0: shared report_style skill, the org wide tone and formatting standard.
* engineering_ops 1.0.0: ticket_standards skill (blocked, at risk, stale, thresholds, escalation, JQL) and the morning_report command, posting to Slack. Depends on house_standards. Bundles Atlassian and Slack connectors.
* _plugin_template: copyable scaffold for new domain plugins, with a worked executive_ops example in its README.

Converted from the earlier markdown task repo. Standards became skills, the morning report task became a namespaced command, connectors are declared by the plugin, and the repo is now a marketplace. Config remains a local per user file.
