# Plugin template

Copy this folder to `plugins/your_domain/` to add a new domain, for example `executive_ops`. Then:

1. Fill in `.claude-plugin/plugin.json`: name, description, keywords. Keep the requires on house_standards unless the domain genuinely needs no shared style.
2. Rename `skills/_skill_template/` to your domain standard, for example `skills/board_metrics/`, and write the definitions. Write the skill description carefully. It is what makes Claude load the skill at the right moment.
3. Rename `commands/_command_template.md` to your command, for example `commands/board_pack.md`. It becomes `/your_domain:board_pack`.
4. Declare any connectors the domain needs, the same way engineering_ops does, in a `.mcp.json` and a `connectors.md`.
5. Register the plugin in `.claude-plugin/marketplace.json` at the repo root by adding an entry to the plugins array.
6. Add a row to the plugin table in the repo README and a note in CHANGELOG.md.

## Worked example: executive_ops

An executive ops plugin for board metrics would hold a `board_metrics` skill (how each metric is defined and calculated, what period it covers, what counts as on track) and a `board_pack` command that pulls those metrics from your sources, assembles the summary, and writes it for review. It would reuse report_style from house_standards so the board pack reads in the same voice as the engineering reports, and reuse the same local config for teams and connectors where they overlap. Nothing in engineering_ops changes when you add it.
