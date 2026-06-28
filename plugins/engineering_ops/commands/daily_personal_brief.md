---
description: Assemble a private morning brief of the email, Slack, Jira, and calendar that needs your attention, and deliver it to yourself as a Slack DM
---

# Daily Personal Brief

Invoked as `/engineering_ops:daily_personal_brief`. Pulls your email, Slack, Jira, and calendar, keeps only what needs your attention, and sends a short digest to you as a Slack direct message. Unlike the morning report, this is a private digest to yourself, not a team report. What counts as attention, the dedup, ranking, and caps come from the `personal_triage` skill; the Jira classification from `ticket_standards`; the tone from `report_style`. This command holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder. This command needs the Jira status mapping (shared with the team report) and your personal values: mail source (`gmail` or `m365`), calendar source (`google` or `m365`), your handles, important senders, Slack scopes, the brief timezone, and the Slack target the brief is delivered to. If `config.md` does not exist, stop and say the system is not configured. If `mail_source` or `calendar_source` is unset or not a recognized value, treat it as missing required config and stop.

**Step 2. Gather** for the window in the `personal_triage` skill, computing "today" and the lookback in the `brief_timezone` from config:
* Mail: branch on `mail_source`. If `gmail`, read unread and recent threads through the Gmail connector. If `m365`, read them through the Microsoft 365 connector. Use only the selected connector.
* Slack: read your direct messages, @-mentions, and the configured channels through the Slack connector.
* Jira: query issues assigned to you through the Atlassian connector, using the assignee-scoped JQL in the personal_triage skill with the status names from the config mapping.
* Calendar: branch on `calendar_source`. If `google`, read today's events through the Google Calendar connector. If `m365`, read them through the Microsoft 365 connector. Use only the selected connector and the calendars named in config.

**Step 3. Process.** Apply the personal_triage skill to decide what surfaces from each source, and ticket_standards for the Jira classification scoped to issues assigned to you. Dedup across sources using the precedence in personal_triage, preferring the native item over a notification email. Rank the few most important things for "Start here", and trim each section to its cap with an overflow count.

**Step 4. Generate** the brief in the shape below, following report_style for tone. Lead with "Start here", then today's calendar, then the email, Slack, and Jira queues. Put any data-incompleteness or guess flag at the very top. Keep the whole brief under 500 words.

**Step 5. Write output.** Save to output/engineering_ops/daily_personal_brief/brief_YYYY_MM_DD.md using today's date in the brief timezone, and overwrite output/engineering_ops/daily_personal_brief/latest.md with the same content.

**Step 6. Distribute.** Send the brief as a Slack direct message to yourself, to the `slack_dm_target` from config. This is a private digest: do not post it to a channel and do not copy anyone else.

**Rules.**
* This brief contains only what your own connected accounts can already see. Never widen distribution: no channel post, no cc, no forward.
* If a required connector is unavailable, stop, write a short note to output/engineering_ops/daily_personal_brief/latest.md explaining what failed, and distribute nothing. The required connectors are Slack, Atlassian, and the selected mail and calendar connectors only. An unauthorized connector you did not select (for example M365 mail when `mail_source` is `gmail`) is not a failure.
* The Microsoft 365 connector is read only. On any M365 branch, never attempt to send mail.
* If you had to guess at anything material, say so at the top of the brief.

## Output shape

---

# Daily Brief, [Weekday, Month Day]

[One line: "as of HH:MM TZ". If any connector failed, data is incomplete, or anything material was guessed, say so here at the very top before anything else.]

## Start here

[The few things to act on first, ranked by the rule in the personal_triage skill. One line each: what it is and the action. Hard cap of five. If nothing needs action today, say so plainly.]

## Today's calendar

[Today's events in time order, from the selected calendar source. One line each: time, title, who. Mark all-day events as such. Flag conflicts and prep-needed. Note the next meeting. If the day is clear, say so in one line.]

## Email

[Threads needing a reply or your attention, grouped sensibly. Use the cap from personal_triage, then a single overflow count, for example "+12 more unread". One line each: sender and subject. If the inbox is clear, say so in one line.]

## Slack

[Direct messages, @-mentions, and active threads needing a response. Same cap and overflow rule. One line each: who and the gist. If nothing is waiting, say so in one line.]

## Jira

[Your blocked, at risk, and stale issues, classified per ticket_standards and scoped to you. One line each: key, short title, and which bucket. Capped. If nothing is flagged, say so in one line.]

---

## Scheduling

This command runs on demand. To run it every morning, open a Cowork task, type `/schedule`, set a weekday morning, and have the scheduled task call `/engineering_ops:daily_personal_brief`. The schedule is set per user. It is not part of the plugin, and a scheduled task runs only while your computer is awake and the desktop app is open.
