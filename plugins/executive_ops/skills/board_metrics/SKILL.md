---
name: board_metrics
description: Definitions for the monthly board metrics pack: the metric catalog (financial, growth, product), key-result health by pace to target, month-over-month trend, the needs-board-attention lens, and the source-query patterns for Looker, NetSuite, and Jira. Load whenever preparing board metrics, an executive summary, or OKR progress for a board pack.
---

# Board metrics, the executive domain standard

The standard the board metrics pack runs against. It is the executive domain's first standard and plays the role `ticket_standards` plays for engineering: it fixes what each metric means, how key result health is judged, and what reaches the board, so every pack reads the same way regardless of who runs it. It contains no personal values. Targets, plan numbers, account ids, look ids, report ids, the OKR project, and field names all come from the local `config.md`. The definitions and thresholds below are the agreed standard; change them here so everyone gets the update, not in any individual config.

For tone and formatting this standard defers to the `report_style` skill from house_standards, the same shared voice every report in this marketplace uses. The board pack leads with what the board must act on, names numbers plainly, and never frames a metric or a key result as an individual's score.

## Cadence and period

The pack is produced monthly. Each run reports:

* **Metrics** for the closed month: the month's value, the change versus the prior month (month over month), and the value versus plan for the month.
* **OKR progress** quarter to date. Objectives and key results are set quarterly, so a monthly pack shows progress through the current quarter, not a full-quarter result. The final pack of a quarter is the one that scores the quarter.

"This month" is the most recent closed calendar month at run time. "Last month" is the month before it. "This quarter" is the fiscal quarter the closed month falls in, with the fiscal year start taken from config.

## Metric catalog

Every metric in the pack is one of the classes below. For each metric the pack states its value, its month-over-month change, and its standing versus plan. Definitions are fixed here; the plan and target numbers are config.

* **Financial** (source: NetSuite). Revenue, gross margin, operating burn, and runway. Revenue is recognized revenue for the closed month. Gross margin is gross profit over revenue for the month. Burn is net cash out for the month. Runway is cash on hand divided by trailing-three-month average burn, expressed in months.
* **Growth** (source: Looker). New ARR, expansion ARR, gross and net revenue retention, logo and revenue churn, and qualified pipeline. ARR figures are end-of-month. Retention and churn are measured over the trailing twelve months unless config sets a different window.
* **Product and operations** (source: Looker). Active accounts or users on the product's agreed activity definition, and the one or two usage measures the business treats as leading indicators. These are system measures, never per-person.

A metric is **on track** when its value meets or beats plan for the period, **behind** when it is under plan but within the at-risk band in the thresholds below, and **off plan** when it is under plan by more than that band. Runway is judged against the runway floor in config, not against a plan line.

## Key result health

Key results are read from Jira (the OKR project and issue types named in config), each carrying a target and a current value. Health is judged by pace to target, comparing actual progress to the progress expected by this point in the quarter. Expected pace is linear across the quarter unless a key result names its own milestone schedule.

The buckets, in order:

* **Achieved.** Current value meets or beats the target. Holds for the rest of the quarter once reached.
* **On track.** At or above 90 percent of expected pace.
* **At risk.** Between 70 and 90 percent of expected pace.
* **Off track.** Below 70 percent of expected pace.
* **Not started.** No measurable progress and the key result's start is already past.

An objective's health is the health of its weakest key result that still matters to the objective: an objective with any off-track key result is off track, otherwise at risk if any key result is at risk, otherwise on track.

## Trend

Trend is how this pack compares to last month's. Compute this month from source. For last month, read the previous run's saved output at `output/executive_ops/board_pack/latest.md` before it is overwritten, and compare the headline metrics and each key result's bucket.

* Show direction only where there is a real prior figure: up, down, or flat against the named prior value, and bucket moves for key results (for example, on track to at risk).
* If no prior pack exists or it cannot be parsed, state "no prior month to compare" once at the top and omit trend for that run. Never infer a trend from a single month. The first run has no trend by definition.

## Needs board attention

The escalation lens for the board. An item belongs here only if it needs the board to know or to act, not merely because a number moved. Include:

* any objective that is off track, or a key result that slipped a bucket since last month (for example on track to off track);
* any metric that is off plan by more than the at-risk band, or whose month-over-month move breaches the material-change threshold in the thresholds below;
* runway below the runway floor in config.

For each, name the metric or objective, the figure and its target or plan, why it needs attention, and the one decision or action that would change it. Keep the section short. A long board-attention list means the bar is set too low.

## Thresholds (standard)

* Key result on track: at or above 90 percent of expected pace.
* Key result at risk: 70 to 90 percent of expected pace.
* Key result off track: below 70 percent of expected pace.
* Metric at-risk band: within 10 percent under plan is behind; more than 10 percent under plan is off plan.
* Material month-over-month change: a move of 10 percent or more in a headline metric, surfaced even when still on plan.
* Runway floor: set per company in config. There is no org-standard runway number.

## Source-query patterns

Generic shapes. Substitute the look, report, project, and field names from config. The pack reads from three systems, and degrades gracefully: if a source is unavailable, mark its section as unavailable rather than dropping it silently, and say so at the top of the pack.

* **Looker** (growth, product): run the look or explore named in config for each metric, scoped to the closed month, and read the measure value. Pull the prior month from the same look for month-over-month where the look does not already return it.
* **NetSuite** (financial): pull the financial report or saved search named in config for the closed month's period, and read revenue, margin inputs, burn, and cash.
* **Jira** (OKRs): query the OKR project named in config for the current quarter's objectives and key results, for example `project = OKR_PROJECT AND issuetype in (OKR_OBJECTIVE_TYPE, OKR_KR_TYPE) AND <quarter field> = currentQuarter`, and read each key result's target and current value from the configured fields.

If a query returns nothing where data is expected, record it as a gap in the pack rather than reporting a zero. A real zero and missing data are different states, and the board needs to know which one it is looking at.
