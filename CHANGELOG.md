# Changelog

Versioned history of the marketplace and its plugins. Local config changes by individuals are not tracked here, only changes to plugins and shared structure.

## marketplace 1.0.0

Initial marketplace.

* house_standards 1.0.0: shared report_style skill, the org wide tone and formatting standard.
* engineering_ops 1.0.0: ticket_standards skill (blocked, at risk, stale, thresholds, escalation, JQL) and the morning_report command, posting to Slack. Depends on house_standards. Bundles Atlassian and Slack connectors.
* _plugin_template: copyable scaffold for new domain plugins, with a worked executive_ops example in its README.

Converted from the earlier markdown task repo. Standards became skills, the morning report task became a namespaced command, connectors are declared by the plugin, and the repo is now a marketplace. Config remains a local per user file.
