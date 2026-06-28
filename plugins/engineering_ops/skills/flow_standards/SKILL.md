---
name: flow_standards
description: Definitions for the weekly flow and risk view across teams: the trailing business week window, throughput, cycle time, aging work in flight, persistent blockers, week-over-week trend, and the weekly escalation lens. Load whenever producing a weekly leadership rollup or multi-team flow summary from Jira.
---

# Flow and risk, the engineering domain standard

The weekly standard every adopter runs against for leadership rollups. It contains no personal values. Team names, project keys, and status names come from `config.md`.

This standard does not restate the status buckets, thresholds, classification, or escalation in the `ticket_standards` skill. It reuses them by pointer and adds only what a weekly view needs that a daily snapshot does not: a week window, throughput, cycle time, aging, and trend. Where this skill and `ticket_standards` both speak, `ticket_standards` is the source for what blocked, at risk, and stale mean, and this skill is the source for how a week is measured.

## The week window

The rollup covers the trailing business week: the five business days ending on the run day, inclusive. "This week" means that window. "Last week" means the equivalent window one week earlier. Run the command once per week on the same weekday so the windows tile cleanly and trend stays comparable. The window is the org standard, not an adopter choice, so every team's numbers cover the same period.

## Throughput

* **Done this week.** Issues that reached the Done bucket (per `ticket_standards`) at any point inside the week window. This is the team's output for the week.
* **Net new this week.** Issues created inside the week window that are already in the Active work bucket, signalling unplanned work that entered mid-week. This reuses the Net new rule in `ticket_standards`, scoped to the week rather than to yesterday.

## Cycle time

Cycle time for an issue is the elapsed business time from when it first entered an Active work status to when it reached the Done bucket. Measure it only for issues counted in Done this week, and report the team average for the week. If a team closed nothing this week, its cycle time is not a number; report it as no completions rather than zero. Cycle time is a flow signal for leadership, never a per engineer measure: report it by team only.

## Aging work in flight

Aging surfaces in-flight work that is getting old without being formally stuck. For each team, the oldest Active work issues by business days since they entered Active, counting any issue at or past the stale threshold in `ticket_standards` as aging. Report the oldest one or two per team by key, title, and age. Aging overlaps with Stale but is framed forward: Stale asks what went quiet, aging asks what has been open too long and may slip.

## Carried risk

The current standing of risk entering the run, using the Blocked, At risk, and Stale definitions and thresholds in `ticket_standards` unchanged:

* **Blocked, at risk, and stale counts** per team, as the daily report classifies them.
* **Persistent blockers.** Any issue that was blocked at both the start and the end of the week window, that is, blocked for the whole week. Persistence is the weekly signal the daily snapshot cannot show, and it is what most often needs a leader.

## Trend

Trend is how this week compares to last week. Compute this week's numbers from Jira. For last week's numbers, read the previous run's saved output at `output/engineering_ops/weekly_leadership_rollup/latest.md` before it is overwritten, and compare the headline figures: done, net new, blocked, at risk, stale, and average cycle time.

* Show direction only where there is a real prior figure to compare: up, down, or flat, against the named prior value.
* If no prior output exists, or it cannot be parsed, state "no prior week to compare" once at the top and omit trend arrows for that run. Never infer a trend from a single week. A first run has no trend by definition.

## Weekly escalation

The weekly escalation lens is broader than the daily one in `ticket_standards`. Start from that rule, then add the two signals only a week reveals. An item or team belongs in the leadership escalation section if any of the following holds:

* it meets the daily escalation rule in `ticket_standards` (blocked and either older than the escalation age or tied to a delivery date inside the escalation date window), or
* it is a persistent blocker, blocked for the whole week window, or
* its team is trending worse on a material measure, for example blocked count up week over week or average cycle time rising past the at risk window.

For each escalation, name the team, the issue or measure, why it needs attention, and the one action that would change it. Keep the section to what needs a leader to act. A long escalation list is a sign the bar is set too low.

## Audience framing

Inherited from `ticket_standards` and the report_style skill: the rollup is read by leaders who care about flow of work and risk, not individual activity. Throughput, cycle time, and aging are team and system measures. Never frame any of them as a per engineer score, and never name an engineer in a leader facing section.

## JQL patterns

Generic shapes that extend the daily ones in `ticket_standards`. Substitute each team's project key from config and the status names from the config mapping. "Start of week" and "end of week" are the bounds of the trailing business week.

* Done this week: `project = KEY AND status changed to (done_statuses) during (startOfWeek, endOfWeek)`
* Net new this week: `project = KEY AND created >= startOfWeek AND status in (active_statuses)`
* Active work in flight: `project = KEY AND status in (active_statuses)`
* Flagged or waiting: `project = KEY AND (Flagged is not EMPTY OR status in (waiting_statuses))`

Cycle time and aging are derived from each issue's status-change history, not from a single query: read when an issue first entered an Active status and when it reached Done. If a query returns nothing for a team, record the team as clear rather than dropping it. A clear board and absent data are different states, and leaders need to know which one they are looking at.
