---
description: {{ONE_LINE_WHAT_THIS_COMMAND_DOES}}
---

# {{Command name}}

Invoked as `/{{plugin_name}}:{{command_name}}`. [One line on what it produces.] Classification and tone come from this plugin's skills and from report_style. This file holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder. Stop if it is missing.

**Step 2. Gather.** [Which connectors to call and what to pull. Reference shared skills for any classification rather than inventing new ones.]

**Step 3. Process.** [How to classify, compute, or summarize. Reuse this domain's standard skill so terms stay consistent.]

**Step 4. Generate** the output in the shape below, following report_style for tone.

**Step 5. Write output.** Save to output/{{plugin_name}}/{{command_name}}/[name]_YYYY_MM_DD.md and overwrite latest.md.

**Step 6. Distribute.** [Where the result goes, by name from config, not hardcoded.]

**Rules.**
* Only include data the recipients can access.
* If a required connector is unavailable, stop, note the failure to latest.md, and distribute nothing.
* Flag anything material that was guessed.

## Output shape

---

# [Title], [date]

## [Lead section]

[What the reader must act on or know first.]

## [Body]

[Supporting detail, grouped sensibly.]

## [Exceptions]

[Anything that could not be handled cleanly, one line each. Omit if empty.]

---

## Scheduling

To run on a cadence, schedule a Cowork task that calls this command. The schedule is per user and runs only while the desktop app is open.
