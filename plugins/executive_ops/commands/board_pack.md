---
description: Assemble the monthly board metrics pack — financials, growth, and quarter-to-date OKR progress with month-over-month trend — and post the summary and board-attention pointer to a private leadership channel
---

# Board Pack

Invoked as `/executive_ops:board_pack`. Once a month, after the books close, assembles the board metrics pack: financial metrics from NetSuite, growth and product metrics from Looker, and OKR progress from Jira, measured against the `board_metrics` skill and compared to last month, written as a board-ready document and summarized to the leadership channel. The metric definitions, key-result health, trend, escalation lens, and source-query patterns come from the board_metrics skill; the tone from `report_style`. This command holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder, for the metric source pointers, OKR source, targets and plan, fiscal year start, runway floor, and the board channel. The board pack uses the executive section of config; it does not need the engineering teams or Jira status mapping. If `config.md` does not exist, stop and say the system is not configured.

**Step 2. Fix the period.** From the board_metrics skill, set the closed month, the prior month, and the current fiscal quarter, using the fiscal year start from config. State the period you settled on at the top of the pack so a reader can check it.

**Step 3. Read last month for trend.** Before pulling anything new, read output/executive_ops/board_pack/latest.md if it exists, and keep its headline metrics and each key result's bucket as last month's baseline. If the file is absent or cannot be parsed, treat this as a run with no prior month to compare, per the board_metrics skill, and carry no baseline.

**Step 4. Gather from the three sources**, each through its connector, using the source-query patterns in the board_metrics skill and the ids and names from config:
* **Looker** for the growth and product metrics: run the configured look or explore for each metric, scoped to the closed month.
* **NetSuite** for the financial metrics: pull the configured report or saved search for the closed month's period, and read revenue, margin inputs, burn, and cash.
* **Jira** for the OKRs: query the configured OKR project and issue types for the current quarter's objectives and key results, reading each key result's target and current value from the configured fields.
If any source is unavailable, mark its section unavailable and note it at the top of the pack rather than dropping it silently.

**Step 5. Compute** using the board_metrics skill:
* Each metric's value, its month-over-month change against the baseline, and its standing versus plan; runway against the runway floor.
* Each key result's health by pace to target, and each objective's health from its key results.
* Trend for the headline metrics and key result buckets against last month's baseline. Show direction only where a real prior figure exists. If there is no baseline, state "no prior month to compare" and omit trend.
* The needs-board-attention list, applying the escalation lens. Keep it to what the board must know or act on.

**Step 6. Generate** the pack in the shape below, following report_style for tone. Lead with the executive summary and the board-attention list. Numbers up front. Keep it to roughly two screens; a board pack is denser than the engineering reports but is still read fastest when it leads with what matters.

**Step 7. Write output.** Save to output/executive_ops/board_pack/pack_YYYY_MM.md using the closed month, and overwrite output/executive_ops/board_pack/latest.md with the same content. Write latest.md only after you have read last month's copy in Step 3, so next month's run has this month as its baseline.

**Step 8. Distribute.** The board pack is primarily the written artifact in output/executive_ops/board_pack/. Post the Executive summary and Needs board attention sections to `board_slack_channel` from config, with a short pointer to the full pack, so leadership sees the headline and a person can carry the pack to the board. Board members are not assumed to be in Slack.

**Rules.**
* Only include data the recipients can access.
* If a required connector is unavailable, follow the board_metrics skill: mark that source's section unavailable and flag it at the top. If NetSuite, Looker, and Jira are all unavailable, stop, write a short note to output/executive_ops/board_pack/latest.md explaining what failed, and distribute nothing. Do not overwrite a good prior latest.md with an empty pack.
* Financial and OKR data are sensitive. Distribute only to the configured board channel and recipients, never more widely.
* Metrics, retention, usage, and key results are company and system measures, never individual performance. Keep per-person framing out of every section.
* If you had to guess at anything material, or trend or a source is unavailable, say so at the top of the pack.

## Output shape

---

# Board Metrics, [Month Year]

[One line stating the period this pack covers: the closed month, and the quarter the OKRs are measured through. If any source was unavailable or trend is missing, say so here in one line.]

## Executive summary

[Three or four sentences. Lead with the headline financials: revenue, burn, runway, each with its month-over-month direction and standing versus plan. Then the overall OKR posture for the quarter to date. Then the single most important thing the board should know this month. If everything is on plan and on track, say so plainly.]

## Needs board attention

[The short list of things that need the board to know or act, per the escalation lens in the board_metrics skill: objectives off track or key results that slipped a bucket, metrics off plan beyond the band or with a material month-over-month move, and runway below the floor. For each: the metric or objective, the figure and its target or plan, why it needs attention, and the one decision or action that would change it. If nothing qualifies, write "Nothing needs board attention this month" and move on.]

## Financial metrics

[Compact table. Source: NetSuite.]

| Metric | This month | MoM | vs plan |
|--------|-----------|-----|---------|
| Revenue | [value] | [trend] | [on track / behind / off plan] |
| Gross margin | [value] | [trend] | [standing] |
| Burn | [value] | [trend] | [standing] |
| Runway | [n months] | [trend] | [vs runway floor] |

## Growth metrics

[Compact table. Source: Looker.]

| Metric | This month | MoM | vs plan |
|--------|-----------|-----|---------|
| New ARR | [value] | [trend] | [standing] |
| Expansion ARR | [value] | [trend] | [standing] |
| Net revenue retention | [value] | [trend] | [standing] |
| Churn | [value] | [trend] | [standing] |
| Qualified pipeline | [value] | [trend] | [standing] |

## OKR progress, quarter to date

[One compact block per objective. Source: Jira.]

### [Objective], [health: on track / at risk / off track]

* [Key result, target, current value, health, and trend versus last month if the bucket moved. One line each.]

## Product and operations

[The one or two leading-indicator usage measures and active accounts or users. Source: Looker. System measures only, never per person. Omit if not configured.]

## Data notes and gaps

[Any source that was unavailable, any metric that returned no data where data was expected, and anything material that was guessed. One line each. Omit the section if everything resolved cleanly.]

---

## Scheduling

This command runs monthly, after month-end close. To run it on a cadence, open a Cowork task, type `/schedule`, set a fixed day each month after close, and have the scheduled task call `/executive_ops:board_pack`. The schedule is set per user, and a scheduled task runs only while your computer is awake and the desktop app is open.
