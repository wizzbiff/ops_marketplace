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
