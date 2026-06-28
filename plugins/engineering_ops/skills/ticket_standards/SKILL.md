---
name: ticket_standards
description: Definitions and thresholds for classifying engineering tickets as blocked, at risk, stale, completed, or net new, plus escalation and audience rules. Load whenever generating any engineering ticket report, sprint summary, or team status from Jira.
---

# Ticket Standards, the engineering domain standard

The common standard for reading and classifying engineering tickets. It contains no personal values. Team names, project keys, and the mapping of real Jira status names come from the local config file the user maintains. The thresholds below are the agreed standard. To change one, change it here so every report and every adopter gets the update.

## Status buckets

Work in four buckets. The user's config maps their real Jira status names onto these.

* Active work: the active statuses from config. Work currently in flight.
* Waiting: the waiting statuses from config. Work that is stuck.
* Done: the done statuses from config. Finished work.
* Backlog: the backlog statuses from config. Not started.

## Thresholds (standard)

* At risk window: 2 business days.
* Stale threshold: 3 business days with no movement.
* Escalation age: a blocked item older than 2 business days.
* Escalation date window: a delivery date inside 3 days.

## Classification rules

Apply in order to every issue pulled.

**Blocked.** True if any of the following holds: its status is in the Waiting bucket, or it carries a flag, or it has an open "is blocked by" link to an issue that is not yet Done.

**At risk.** An Active work issue that is not blocked but is in danger: it has a due date within the at risk window and is not in the final Active status, or it sits in a sprint ending within the at risk window with meaningful work remaining.

**Stale.** An Active work issue with no status change and no comment for the stale threshold or longer. Stale is not blocked. It usually means forgotten, not impeded, and is surfaced separately.

**Completed yesterday.** Any issue moved into the Done bucket since the previous business day.

**Net new.** Any issue created since the previous business day that is already in the Active work bucket. Signals unplanned work entering mid sprint.

**Needs review.** If an issue does not fit cleanly, place it here with a one line reason rather than guessing.

## Escalation rules

An item belongs in an escalation section only if it is blocked AND either older than the escalation age or tied to a delivery date inside the escalation date window. For each escalation, name the team, the issue, why it is stuck, and the one action that would unblock it. Do not list every blocked ticket, only the ones that need a leader to act.

## Audience

These reports are read by leaders who care about flow of work and risk, not individual activity. Follow the report_style skill from house_standards for tone. Never include per engineer performance framing.

## JQL patterns

Generic query shapes. Substitute each team's project key from config and the status names from the config mapping.

* Active work: `project = KEY AND status in (active_statuses)`
* Changed since yesterday: `project = KEY AND updated >= -1d`
* Done since yesterday: `project = KEY AND status changed to (done_statuses) after -1d`
* Flagged or waiting: `project = KEY AND (Flagged is not EMPTY OR status in (waiting_statuses))`

If a query returns nothing for a team, record the team as clear rather than dropping it. Absence of data and a clear board are different states.
