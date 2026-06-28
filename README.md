# Ops Marketplace

A private Claude Cowork plugin marketplace. It houses one shared standards plugin and one plugin per domain. Engineering ops and executive ops ship today. Any further domain drops in beside them without disturbing what already works.

## How the ops_automations repo maps onto plugins. See the ops_automations repo for reference: https://github.com/wizzbiff/ops_automations

* The shared standards become skills. `style` is now the `report_style` skill in house_standards. The ticket definitions are now the `ticket_standards` skill in engineering_ops.
* Each task becomes a namespaced slash command. The morning report is now `/engineering_ops:morning_report`.
* The connectors are declared by the plugin instead of wired by hand.
* The repo itself is now a marketplace, so people install instead of clone, and updates flow when you merge a PR.
* Config stays exactly where it was: a local `config.md` each person keeps in their own working folder, never committed.

## Layout

```
ops_marketplace/
  .claude-plugin/
    marketplace.json            lists every plugin in the repo
  README.md
  CONTRIBUTING.md
  CHANGELOG.md
  .gitignore
  config/
    config.example.md           copy to a local config.md. shared by all plugins. never committed
  output/                       generated results, ignored by git
  plugins/
    house_standards/            shared report_style skill. install alongside any domain
    engineering_ops/            ticket_standards skill plus the morning_report command
    _plugin_template/           copy this to add a domain, for example executive_ops
```

Each plugin is self contained: its own `.claude-plugin/plugin.json`, its `skills/` as `SKILL.md` folders, its `commands/` as flat markdown files, and its connector declarations. The one shared thing, house style, is its own small plugin so it is never duplicated.

## Plugins

| Plugin | What it is | Commands | Connectors |
|--------|-----------|----------|------------|
| house_standards | shared report tone and formatting | none, skills only | none |
| engineering_ops | ticket health, personal triage, research, and flow standards | /engineering_ops:morning_report, /engineering_ops:daily_personal_brief, /engineering_ops:ticket_research, /engineering_ops:weekly_leadership_rollup | Atlassian, Slack, Gmail or M365, Google Calendar or Outlook, GitHub |
| executive_ops | board metrics standard and the monthly board pack | /executive_ops:board_pack | Looker, NetSuite, Atlassian, Slack |

## Adopt it

1. Add this repo as a marketplace in Cowork. Open Customize, go to the Plugins tab, add a marketplace, and point it at this GitHub repo. On a Team or Enterprise plan an owner does this once for everyone in Organization settings under Plugins.
2. Install house_standards and engineering_ops.
3. Authorize the Atlassian and Slack connectors. See plugins/engineering_ops/connectors.md.
4. Copy config/config.example.md to a `config.md` in your Cowork working folder and fill it in.
5. Run `/engineering_ops:morning_report` once by hand, fix the status mapping in config, then schedule a daily Cowork task that calls it.

## Add a new domain, for example executive_ops

1. Copy `plugins/_plugin_template/` to `plugins/executive_ops/`.
2. Fill in its plugin.json, write its standard as a skill, and write its commands.
3. Add an entry to `.claude-plugin/marketplace.json`.
4. Add a row to the plugins table above and a CHANGELOG note.

The shared style and the local config carry over, so the new domain's reports match the voice of the existing ones from day one. See the template README for a worked executive_ops example.

## Honest notes, please read before you commit to this

* **Research preview.** Cowork plugin support is a research preview and the format moves. Field names in the manifests and especially the way connectors attach are the parts most likely to change. Verify the manifests against the current Cowork plugin reference before you rely on them.
* **Connectors.** The connector declaration is the least settled part of this repo. For managed connectors like Slack and Atlassian, the robust path today is authorizing them in the Cowork connector directory and bundling through the Customize flow. The `.mcp.json` is there for declaring a remote MCP endpoint if you go that route.
* **Org distribution.** A private marketplace that installs automatically across a team needs a Team or Enterprise plan with Cowork and Skills enabled, and the org sync currently fetches external sources only from public repos. Confirm with whoever owns your plan.
* **Scheduling is not packaged.** Plugins ship the command. Turning it into a daily run is still a per user `/schedule` action, and a scheduled task runs only while the machine is awake and the app is open.
* **Identifiers.** Plugin, skill, and command names here use underscores. The wider ecosystem often uses hyphens, for example `engineering-ops`. Both work. Rename if you prefer the hyphen convention.
* **Frontmatter.** The only triple dash sequences in this repo are the `---` fences at the top of each SKILL.md and command file. Those are mandatory YAML frontmatter syntax. The files will not parse without them.
