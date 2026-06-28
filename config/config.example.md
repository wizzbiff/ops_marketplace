# Config

Copy this file to `config.md` in your Cowork working folder, then fill in every value. `config.md` is local to you and is ignored by git, so your real keys and channels are never committed. It is the only file you edit to adopt these plugins, and it is shared across every plugin in this marketplace.

Each value is written as `{{UPPER_SNAKE_CASE}}`. Replace the whole token, braces included.

## Teams

One row per team. Add or remove rows freely. Leave Board id empty for any team that does not run sprints.

| Team | Manager | Project key | Board id | Runs sprints |
|------|---------|-------------|----------|--------------|
| {{TEAM_NAME}} | {{TEAM_MANAGER}} | {{PROJECT_KEY}} | {{BOARD_ID}} | {{RUNS_SPRINTS}} |
| {{TEAM_NAME}} | {{TEAM_MANAGER}} | {{PROJECT_KEY}} | {{BOARD_ID}} | {{RUNS_SPRINTS}} |

Filled example, delete in your real config:

| Team | Manager | Project key | Board id | Runs sprints |
|------|---------|-------------|----------|--------------|
| Data | Jordan Lee | DATA | 412 | yes |
| Security | Priya Shah | SEC | | no |

## Jira status mapping

Map your real Jira status names onto the four buckets the standard uses. List the exact names, comma separated.

```yaml
active_statuses:  [{{ACTIVE_STATUSES}}]
waiting_statuses: [{{WAITING_STATUSES}}]
done_statuses:    [{{DONE_STATUSES}}]
backlog_statuses: [{{BACKLOG_STATUSES}}]
```

Filled example:

```yaml
active_statuses:  ["In Progress", "In Review"]
waiting_statuses: ["Blocked", "On Hold", "Waiting on Customer"]
done_statuses:    ["Done", "Closed", "Resolved"]
backlog_statuses: ["To Do", "Backlog"]
```

## Distribution

```yaml
report_slack_channel: {{REPORT_SLACK_CHANNEL}}
direct_recipients:    [{{DIRECT_RECIPIENTS}}]
```

Leave the brackets empty to post only to the channel.

The sections above are everything `morning_report` needs. The sections below are used only by specific commands. Fill in a section only if you run the command that uses it; skip the rest.

---

## Daily personal brief

Used by `/engineering_ops:daily_personal_brief`. Personal values specific to you. The brief also reuses the Jira status mapping above.

```yaml
# Sources. Each is independent: Gmail mail with Outlook calendar is fine.
mail_source:     {{MAIL_SOURCE}}      # gmail or m365
calendar_source: {{CALENDAR_SOURCE}}  # google or m365
calendars:       [{{CALENDARS}}]      # which calendars to read. empty = your primary only

# Identity, so "to me", "mentions me", and "assigned to me" resolve.
email_address:   {{EMAIL_ADDRESS}}
slack_handle:    {{SLACK_HANDLE}}
jira_account:    {{JIRA_ACCOUNT}}     # leave as currentUser to use the connected Jira account

# What to surface.
important_senders: [{{IMPORTANT_SENDERS}}]   # emails or names that always surface
brief_slack_channels: [{{BRIEF_SLACK_CHANNELS}}]  # channels to scan beyond DMs and mentions
slack_keywords:    [{{SLACK_KEYWORDS}}]      # words that surface a channel message

# Timing and delivery.
brief_timezone:  {{BRIEF_TIMEZONE}}    # IANA name, e.g. America/New_York. controls "today" and the lookback
lookback:        {{LOOKBACK}}          # e.g. "since previous business day"
slack_dm_target: {{SLACK_DM_TARGET}}   # where the brief is delivered. your own handle, for a DM to yourself
```

Filled example, for reference only. Delete it in your real config.

```yaml
mail_source:     gmail
calendar_source: google
calendars:       []
important_senders: ["ceo@example.com", "Jordan Lee"]
brief_slack_channels: ["incidents", "team_eng"]
slack_keywords:    ["prod down", "release"]
brief_timezone:  America/New_York
lookback:        since previous business day
slack_dm_target: "@rob"
```

---

## Ticket research

Used by `/engineering_ops:ticket_research`.

```yaml
github_repos: [{{GITHUB_REPOS}}]        # owner/repo entries. empty = skip GitHub; never searches beyond this list
research_confluence_spaces: [{{CONFLUENCE_SPACES}}]  # space keys to prioritize. empty = follow links only
research_slack_channels:    [{{RESEARCH_SLACK_CHANNELS}}]  # channels to search for the key and title. empty = skip
post_jira_comment: {{POST_JIRA_COMMENT}}   # true to post a pointer comment on the ticket, false to write the file only
code_context_cap:  {{CODE_CONTEXT_CAP}}    # max PRs or files to detail before summarizing, e.g. 5
ticket_key_pattern: {{TICKET_KEY_PATTERN}} # optional, to parse the key from a URL or text, e.g. [A-Z]+-[0-9]+
```

Filled example:

```yaml
github_repos: ["acme/web", "acme/api"]
research_confluence_spaces: ["ENG"]
research_slack_channels:    ["team_eng"]
post_jira_comment: true
code_context_cap:  5
ticket_key_pattern: "[A-Z]+-[0-9]+"
```

---

## Weekly leadership rollup

Used by `/engineering_ops:weekly_leadership_rollup`. Optional overrides only. The rollup otherwise reuses the teams, status mapping, and distribution above.

```yaml
rollup_slack_channel: {{ROLLUP_SLACK_CHANNEL}}   # leave unset to fall back to report_slack_channel
teams_filter:         [{{ROLLUP_TEAMS_FILTER}}]  # limit to named teams. empty = every team above
```

---

## Board pack (executive)

Used by `/executive_ops:board_pack`. This command reads only this section; the engineering teams and status mapping above do not apply to it. The values here are sensitive (ids, financials, targets), so keep this `config.md` local and uncommitted as always.

```yaml
fiscal_year_start_month: {{FISCAL_YEAR_START_MONTH}}   # 1-12, the month your fiscal year begins
runway_floor_months:     {{RUNWAY_FLOOR_MONTHS}}       # board-attention trigger when runway falls below this

looker:
  base_url:            {{LOOKER_BASE_URL}}
  growth_looks:        # metric name -> look id or explore reference
    new_arr:           {{LOOK_NEW_ARR}}
    expansion_arr:     {{LOOK_EXPANSION_ARR}}
    net_revenue_retention: {{LOOK_NRR}}
    churn:             {{LOOK_CHURN}}
    qualified_pipeline: {{LOOK_PIPELINE}}
  product_looks:
    active_accounts:   {{LOOK_ACTIVE_ACCOUNTS}}
    leading_indicator: {{LOOK_LEADING_INDICATOR}}

netsuite:
  account:             {{NETSUITE_ACCOUNT_ID}}
  financial_report:    {{NETSUITE_REPORT_REF}}          # report or saved search for monthly financials

okrs:
  jira_project:        {{OKR_JIRA_PROJECT}}
  objective_issuetype: {{OKR_OBJECTIVE_TYPE}}
  key_result_issuetype: {{OKR_KR_TYPE}}
  quarter_field:       {{OKR_QUARTER_FIELD}}
  target_field:        {{OKR_TARGET_FIELD}}
  current_field:       {{OKR_CURRENT_FIELD}}

plan:                  # the plan or target each metric is judged against. definitions live in the board_metrics skill
  revenue:             {{PLAN_REVENUE}}
  gross_margin:        {{PLAN_GROSS_MARGIN}}
  burn:                {{PLAN_BURN}}
  new_arr:             {{PLAN_NEW_ARR}}
  expansion_arr:       {{PLAN_EXPANSION_ARR}}
  net_revenue_retention: {{PLAN_NRR}}
  churn:               {{PLAN_CHURN}}
  qualified_pipeline:  {{PLAN_PIPELINE}}

board_slack_channel: {{BOARD_SLACK_CHANNEL}}   # a private leadership channel; the pack holds sensitive data
board_direct_recipients: [{{BOARD_DIRECT_RECIPIENTS}}]   # optional. empty to post only to the channel
```
