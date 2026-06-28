---
description: Given a Jira ticket, gather its links, related issues, GitHub code, and Slack threads into a ready-to-start brief, and optionally post a pointer comment back to the ticket
---

# Ticket Research

Invoked as `/engineering_ops:ticket_research`. On demand, not scheduled: you give it a ticket and it produces a ready-to-start brief. Provide the ticket as a Jira key (for example PROJ-123) or a full issue URL when you run the command, for example `/engineering_ops:ticket_research PROJ-123`. What a brief contains and how to gather it come from the `research_standards` skill, related-issue status labels from `ticket_standards`, and tone from `report_style`. This command holds the orchestration and the output shape.

## Run

**Step 1. Load config.** Read `config.md` from the current working folder, for the GitHub repo allowlist, search scopes, the comment toggle, and the caps. If `config.md` does not exist, stop and say the system is not configured. Parse the ticket key from the input using `ticket_key_pattern` from config if set; if no key can be extracted, stop and say so rather than guessing.

**Step 2. Gather** following the research_standards skill:
* Jira, **required**, through the Atlassian connector. Resolve the ticket, its fields and comments, and follow its issue links one hop only. If Atlassian is unavailable, stop: without the ticket there is no brief.
* Confluence, enrichment, through the Atlassian connector. Pull linked docs from the ticket and its epic, scoped to `confluence_spaces` if set.
* GitHub, enrichment, through the GitHub connector. Search only the repos in `github_repos` for the ticket key in branch names, PR titles, and commit messages. If the allowlist is empty, skip GitHub and note it. Never search beyond the allowlist.
* Slack, enrichment, through the Slack connector. Search `slack_channels` for the key and title. If empty, skip and note it.

If an enrichment source is unavailable or its scope is empty, degrade gracefully: build the brief from what you have and flag the gap at the top. Only Atlassian (Jira) read is required.

**Step 3. Process.** Synthesize the gathered material into the brief per research_standards. Keep facts and inferences distinct and flag every inference, including draft acceptance criteria and the suggested starting point. Label related-issue status with ticket_standards and mark unconfirmed PR matches as such. Apply the section caps, honouring `code_context_cap` for code touchpoints.

**Step 4. Generate** the brief in the shape below, following report_style for tone. Put any data-incompleteness or guess flag at the very top. Aim to stay under about 800 words excluding linked references.

**Step 5. Write output.** Save to output/engineering_ops/ticket_research/<KEY>_YYYY_MM_DD.md using the ticket key and today's date, and overwrite output/engineering_ops/ticket_research/latest.md with the same content.

**Step 6. Distribute.** If `post_jira_comment` is true, post a short pointer comment on the input ticket through the Atlassian connector. The comment is a notification, not the brief: a few plain lines (problem, one or two prior-art links, suggested starting point, and the output file path), ending with the signature token `[ticket_research:brief]`. Do not paste restricted Slack, Confluence, or private-repo content into the comment. Before posting, read the ticket's existing comments and look for one by the connected account containing `[ticket_research:brief]`: if found, edit it in place, or skip if the connector cannot edit; if not found, post one. Never create a duplicate. Record the comment action, posted, updated, or skipped, in the output file. If `post_jira_comment` is false, write the file only.

**Rules.**
* Required connector: Atlassian (Jira) read. Confluence, GitHub, and Slack are enrichment; degrade gracefully and flag gaps rather than stopping.
* The only write this command performs is exactly one comment on the input ticket. Never comment on related or linked issues. Never edit ticket fields, status, assignee, or links. Never @-mention anyone. Honour the `post_jira_comment` toggle.
* Never search GitHub beyond the allowlist. Never widen distribution beyond the input ticket and the output file.
* Do not fabricate missing docs, PRs, or threads. Distinguish searched-and-found-nothing from could-not-search.
* If you had to guess or infer anything material, flag it at the top of the brief, and in the comment if one is posted.

## Output shape

---

# Ready-to-start brief: [KEY] — [ticket title]

[One line: "as of HH:MM". If any enrichment source failed, a scope was empty, or anything material was synthesized or guessed, say so here at the very top before anything else.]

## Summary

[Two or three lines: what is being asked, and the current state.]

## Problem statement

[From the ticket. What needs to change and why.]

## Acceptance criteria

[The ticket's own criteria if present. If absent, a synthesized draft, clearly flagged as synthesized.]

## Prior art and context

[Linked Confluence docs and related issues, each with a link and one line on relevance. Label each related issue's status, active or done. Capped, with an overflow count. Omit cleanly if nothing was found, but say so.]

## Code touchpoints

[Related repos, PRs, branches, commits, and the files they touch, from the allowlist only. Each: link and one line on why it is relevant. Mark unconfirmed matches as possible, unconfirmed; mark archived-repo hits as archived. Summarize, do not inline large diffs. Capped by code_context_cap, with an overflow count.]

## Relevant discussions

[Slack threads about the ticket, each a link and a one-line gist. Capped, with an overflow count.]

## Open questions and risks

[Unknowns, blockers, and ambiguities that would slow a start. Flag any that are inferred rather than stated in the ticket.]

## Suggested starting point

[One short paragraph, clearly marked as a suggestion, not an instruction. Where you would begin given the above.]

## Sources and gaps

[What was searched, found, empty, or inaccessible. Distinguish searched-and-found-nothing from could-not-search. Record the Jira comment action: posted, updated, or skipped.]

---

## Scheduling

This command runs on demand, per ticket. There is nothing to schedule.
