---
description: Generate the morning team ticket report from Jira and post the leader facing summary to Slack
---

# Morning Report

Invoked as `/engineering_ops:morning_report`. Generates the morning ticket report for every team and posts the summary to Slack. The classification logic lives in the `ticket_standards` skill and the tone in the `report_style` skill, both loaded automatically. This command holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder. It holds teams, the Jira status mapping, and distribution. If it does not exist, stop and say the system is not configured, and point the user to config.example.md in the marketplace repo.

**Step 2. Pull from Jira** through the Atlassian connector. For every team in config, query its project and board using the JQL patterns in the ticket_standards skill, substituting that team's project key and the status names from the config mapping. Pull active work, issues changed since the previous business day, issues moved to Done since the previous business day, and flagged or waiting issues. If a query returns nothing, record the team as clear rather than dropping it.

**Step 3. Classify** every issue using the rules and thresholds in the ticket_standards skill: blocked, at risk, stale, completed yesterday, net new, or needs review. Do not invent a status. Place anything ambiguous under needs review with a one line reason.

**Step 4. Generate** the report in the shape below, following the report_style skill for tone. Lead with the summary and the escalation list. Apply the escalation rules from ticket_standards so the escalation section holds only items that need a leader to act. Keep the whole report under 500 words.

**Step 5. Write output.** Save to output/engineering_ops/morning_report/report_YYYY_MM_DD.md using today's date, and overwrite output/engineering_ops/morning_report/latest.md.

**Step 6. Distribute.** Post the Summary and Needs escalation sections to the report Slack channel from config, with a short pointer to the full report. Send to direct recipients only if config lists any.

**Rules.**
* Only include data from boards and channels the recipients can access.
* If the Atlassian or Slack connector is unavailable, stop, write a short note to latest.md explaining what failed, and post nothing.
* Never include per engineer performance framing in leader facing sections.
* If you had to guess at anything material, say so at the top.

## Output shape

---

# Engineering Morning Report, [Weekday, Month Day]

## Summary

[Two or three sentences. Headline numbers across all teams: active, blocked, closed since yesterday. Then the single most important thing a leader should know today. If everything is healthy, say so plainly.]

## Needs escalation

[Items that need a leader to act, per the escalation rules. For each: team, issue key and title, why it is stuck, the one unblocking action. If none, write "Nothing needs escalation today".]

## By team

[One compact block per team.]

### [Team name], [Manager]

* Active: [n]   Blocked: [n]   At risk: [n]   Closed since yesterday: [n]
* Blocked: [issue key, short title, one phrase on why. Omit if zero.]
* At risk: [issue key, short title, one phrase on the risk. Omit if zero.]
* Sprint health: [only if the team runs sprints: days left, percent of points remaining, on track or not. Omit otherwise.]

## Stale work

[Issues gone quiet, grouped by team, key and title only. No commentary.]

## Needs review

[Anything that could not be classified, one line reason each. Omit if empty.]

---

## Scheduling

This command runs on demand. To run it every morning, open a Cowork task, type `/schedule`, set a weekday morning before standup, and have the scheduled task call `/engineering_ops:morning_report`. The schedule is set per user. It is not part of the plugin, and a scheduled task runs only while your computer is awake and the desktop app is open.
