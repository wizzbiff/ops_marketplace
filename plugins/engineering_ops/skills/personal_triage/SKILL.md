---
name: personal_triage
description: Definitions for what counts as "needs my attention" across email, Slack, Jira, and calendar, plus dedup, ranking, and item caps. Load whenever assembling a personal daily brief or triage digest of someone's own inbox, messages, issues, and schedule.
---

# Personal triage, the engineering domain standard

What counts as "needs my attention" across email, Slack, Jira, and calendar, and how to rank and trim it. This is the standard the `daily_personal_brief` command runs against. It contains no personal values. Handles, important senders, channels, and timezone come from the local `config.md`. The rules below are the agreed definition of attention, so a change here updates every personal brief at once.

This is a *personal* triage standard, not a team or leader standard. Where it covers Jira, it reuses the `ticket_standards` skill rather than redefining anything.

## What needs attention, by source

**Email.** Surface a message if it is unread and any of these hold:
* it is addressed directly to me (to or cc), or
* it is from an important sender listed in config, or
* it is a thread awaiting my reply (I am the last expected responder).

Suppress newsletters, marketing, and automated notifications. In particular, suppress automated notification mail from Jira, Slack, and GitHub: that content is surfaced natively from those sources, so an email copy is duplication. See dedup below.

**Slack.** Surface a message if it is unread and any of these hold:
* it is a direct message to me, or
* it @-mentions me, or
* it is in a thread I am already participating in, or
* it matches a configured keyword.

Suppress general channel chatter I was not addressed in.

**Jira.** Apply the Blocked, At risk, and Stale classification and thresholds from the `ticket_standards` skill (at risk window 2 business days, stale threshold 3 business days), scoped to issues assigned to me. Reference those thresholds by pointer; do not restate the numbers here, so a change to ticket_standards propagates automatically. Only those three buckets carry over. Explicitly exclude Completed yesterday (not actionable), Net new, and Needs escalation (leader concepts, wrong audience for a personal brief).

Classification reads the Jira status mapping from `config.md`, the same mapping the team report uses. The personal query shape is assignee-scoped and lives here, not in ticket_standards, because an assignee filter is personal-triage-specific:
* My open work: `assignee = currentUser() AND status not in (done_statuses)`
* My flagged or waiting: `assignee = currentUser() AND (Flagged is not EMPTY OR status in (waiting_statuses))`
* My recently changed: `assignee = currentUser() AND updated >= -3d`

**Calendar.** Surface today's events. Rules are source-agnostic and apply to whichever provider config selects (Google Calendar or Outlook):
* Include events I have accepted or marked tentative. Exclude events I have declined.
* Handle all-day and multi-day events: list them once, noted as all-day, not as a timed slot.
* Flag conflicts: any two accepted events whose times overlap.
* Flag prep-needed as a heuristic only: external attendees, an attached document, or no agenda. Flag it; never fabricate prep that is not evidenced.

## Dedup across sources

The same thing can appear more than once. Prefer the native source and suppress the copy:
* Native Jira item over a Jira notification email.
* Native Slack message over a Slack notification email.

When the same underlying item still surfaces in two sections, keep it in the most actionable one and drop the duplicate.

## Ranking for "Start here"

The brief opens with the few things to act on first. Rank deterministically, highest first:
1. A direct message or @-mention awaiting my reply.
2. A calendar item starting soon that needs preparation.
3. My blocked or at risk Jira tied to something due today.
4. A direct email awaiting my reply.

Within a tier, order by time: soonest deadline or oldest waiting first.

## Volume control

A personal inbox is far larger than a team board, so length is controlled by item caps, not prose. Each source section shows at most the top few items by the ranking above, then a single overflow count, for example "+12 more unread". "Start here" is capped at five. Trimming to the cap is the standard, not a failure to report.

## Clear state

If a source has nothing that needs attention, say so in one line rather than omitting it silently, so an empty inbox and an unread connector read differently. If nothing across any source needs attention, say that plainly at the top.

## Time windows

"Since the previous business day" means the last working day: on a Monday it covers Friday through Sunday. Compute today and all windows in my timezone from config, not the runtime's timezone, so "today" on the calendar and the lookback windows do not drift by a day. Holidays are out of scope.

## Audience

Follow the report_style skill from house_standards for tone. This brief is private to the person who runs it: never widen distribution, and never frame anything as someone else's performance.
