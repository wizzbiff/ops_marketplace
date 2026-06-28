# Contributing

This repo is a shared standard distributed as plugins. Changes to skills and standards affect everyone who installs them, so they are deliberate and versioned.

## Source of truth and keeping in sync

This marketplace is a *derived packaging*, not the source. The source of truth is the sibling repo **ops_automations**, a folder library where the same standards and tasks are authored as plain markdown. Here they are repackaged as plugins: standards become skills, tasks become namespaced commands.

* **Author upstream, not here.** Make standard and task changes in ops_automations first, then port them into this repo. Do not edit a skill or command here and expect it to flow back; this repo is downstream.
* **Drift is silent.** Nothing links the two repos automatically. When a standard changes upstream, the matching skill here is stale until someone re-ports it by hand. Treat any library change as a to-do against this repo.
* **A port is a transformation, not copy-paste.** A standard file becomes a SKILL.md whose description triggers loading; a task plus its output template become a single command file; repo-relative path pointers become skill-name references; and the library's per-task config files collapse into this repo's single shared config.example.md as sections. Expect judgement each time.
* **Parity is a snapshot.** When a sync lands (most recently PR #1, 2026-06-28), the two repos match at that moment only. Re-sync deliberately when the library moves. This repo is intentionally a periodically refreshed mirror, kept parked until its standards settle and real installers exist.

## Conventions

* **Placeholders.** Every value an adopter supplies is `{{UPPER_SNAKE_CASE}}` and lives only in config.example.md or in a template file. Never in a skill or a committed manifest.
* **No secrets committed.** Real project keys, board ids, channels, and handles go only in a local config.md, which git ignores.
* **Self contained plugins.** A plugin carries its own skills and commands. The only thing it may rely on from outside is house_standards, declared in its requires.
* **Output.** Commands write to output/plugin_name/command_name/. The output folder is ignored by git.

## Changing a standard

Editing a skill in house_standards or a domain plugin changes behavior for everyone who installs it. So:

1. Open the change for review rather than committing directly.
2. Bump the affected plugin's version in its plugin.json.
3. Add a CHANGELOG entry.
4. After merge, the marketplace sync pushes the new version to installers. Their local config is untouched.

If only your team needs a different value, that usually belongs in local config, not a fork of the skill.

## Adding a plugin

1. Copy plugins/_plugin_template/ to plugins/your_domain/.
2. Write the standard as a skill and the workflows as commands. Reuse report_style and any existing shared skills.
3. Declare connectors the same way engineering_ops does.
4. Register the plugin in .claude-plugin/marketplace.json.
5. Update the plugins table in the README and add a CHANGELOG note.

## Adding a command to an existing plugin

Drop a markdown file in that plugin's commands/. It becomes `/plugin_name:file_name`. Reuse the plugin's skills rather than restating definitions. Bump the plugin version.

## Ready to ship checklist

A plugin is ready when: each skill has a clear description that loads it at the right time, commands load context before acting and stop safely if a connector is missing, commands write to their own output folder, and nothing in the committed files contains personal values or secrets.
