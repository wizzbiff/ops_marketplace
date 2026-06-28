---
description: Generate the weekly flow and risk rollup across teams from Jira — throughput, cycle time, aging, persistent blockers, and week-over-week trend — and post the leadership summary to Slack
---

# Weekly Leadership Rollup

Invoked as `/engineering_ops:weekly_leadership_rollup`. Generates the weekly flow and risk rollup across teams and posts the summary to Slack. Run it once a week on the same weekday so the week windows tile cleanly. The week window, throughput, cycle time, aging, trend, and the weekly escalation lens come from the `flow_standards` skill; the status buckets, thresholds, and classification from `ticket_standards`; the tone from `report_style`. This command holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder, for teams, the Jira status mapping, and distribution. Optional keys `rollup_slack_channel` (a leadership-only channel) and `teams_filter` (a subset of teams) override the defaults if present. If `config.md` does not exist, stop and say the system is not configured.

**Step 2. Read last week for trend.** Before pulling anything new, read output/engineering_ops/weekly_leadership_rollup/latest.md if it exists, and keep its headline figures (done, net new, blocked, at risk, stale, average cycle time) as last week's baseline. If the file is absent or cannot be parsed, treat this as a run with no prior week to compare, per flow_standards, and carry no baseline.

**Step 3. Pull from Jira** through the Atlassian connector. For every team in config, query its project and board using the JQL patterns in flow_standards and ticket_standards, substituting that team's project key and the status names from the config mapping. For the trailing business week defined in flow_standards, pull: work done this week, net new this week, active work in flight, and flagged or waiting issues. Read each issue's status-change history where you need it, so you can compute cycle time for issues done this week and age for work in flight. If a query returns nothing for a team, record the team as clear rather than dropping it.

**Step 4. Compute the week** using flow_standards, and classify in-flight work using ticket_standards:
* Throughput: done this week and net new this week, per team.
* Cycle time: the team average over issues done this week. If a team closed nothing, report no completions, not zero.
* Aging: the oldest one or two in-flight issues per team at or past the stale threshold.
* Carried risk: blocked, at risk, and stale counts per team, plus any persistent blocker that was blocked across the whole week.
* Trend: compare this week's headline figures to last week's baseline from Step 2. Show direction only where a real prior figure exists. If there is no baseline, state "no prior week to compare" and omit trend arrows.
Place anything that does not fit cleanly under needs review with a one line reason. Do not invent a status or a trend.

**Step 5. Generate** the rollup in the shape below, following report_style for tone. Lead with the summary and the leadership escalation list. Apply the weekly escalation lens in flow_standards so the escalation section holds only what needs a leader to act: persistent blockers, slipping dates, and teams trending worse. Keep the whole rollup under 600 words.

**Step 6. Write output.** Save to output/engineering_ops/weekly_leadership_rollup/rollup_YYYY_MM_DD.md using today's date, and overwrite output/engineering_ops/weekly_leadership_rollup/latest.md with the same content. Write latest.md only after you have read last week's copy in Step 2, so next week's run has this week as its baseline.

**Step 7. Distribute.** Post the Summary and Risks needing leadership action sections to the rollup channel, with a short pointer to the full rollup. Use `rollup_slack_channel` from config if set, otherwise `report_slack_channel`. Send to `direct_recipients` only if config lists any.

**Rules.**
* Only include data from boards and channels the recipients can access.
* If the Atlassian or Slack connector is unavailable, stop. Write a short note to output/engineering_ops/weekly_leadership_rollup/latest.md explaining what failed and post nothing. Do not overwrite a good prior latest.md with a partial week; if you must write the failure note, say plainly that it replaces the prior rollup.
* Never include per engineer performance framing in any leader facing section. Throughput, cycle time, and aging are team and system measures, never individual scores.
* If you had to guess at anything material, or trend is unavailable, say so at the top of the rollup.

## Output shape

---

# Engineering Weekly Rollup, week ending [Weekday, Month Day]

## Summary

[Two or three sentences. Headline flow numbers across all teams: done this week, net new, and how many are blocked, at risk, or stale, each with its trend arrow against last week where there is one. Then the single most important thing leadership should know this week. If there is no prior week, say "no prior week to compare" here and drop the arrows. If everything is healthy and flowing, say so plainly.]

## Risks needing leadership action

[The short list of items and teams that need a leader to act, per the weekly escalation lens in flow_standards: persistent blockers blocked all week, items tied to a slipping delivery date, and teams trending worse. For each: team, the issue key and title or the measure, why it needs attention, and the one action that would change it. If nothing qualifies, write "Nothing needs leadership action this week" and move on.]

## Flow by team

[One compact block per team in config.]

### [Team name], [Manager]

* Done: [n] ([trend])   Net new: [n]   Avg cycle time: [n days or "no completions"] ([trend])
* Risk: Blocked [n] ([trend])   At risk [n]   Stale [n]
* Oldest in flight: [key, short title, age in days. Omit the line if nothing is aging.]
* Persistent blocker: [key, short title, one phrase on why, if anything was blocked all week. Omit if none.]
* Sprint health: [only if the team runs sprints in config: days left, percent of points remaining, on track or not. Omit otherwise.]

## Aging and stale work

[In-flight work getting old and work gone quiet, grouped by team, key and title and age only. The nudge list. No commentary.]

## Net new and unplanned

[Work that entered the active bucket mid-week, grouped by team, key and title only. Signals unplanned load. Omit the section if there was none.]

## Needs review

[Anything that could not be classified, one line reason each. Omit the section if empty.]

---

## Scheduling

This command runs weekly. To run it every week, open a Cowork task, type `/schedule`, set the same weekday each week, and have the scheduled task call `/engineering_ops:weekly_leadership_rollup`. The schedule is set per user, and a scheduled task runs only while your computer is awake and the desktop app is open.
