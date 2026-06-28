---
name: research_standards
description: Definitions for what a ready-to-start ticket brief contains and how to gather it, covering ticket resolution, one-hop links, Confluence and Slack discovery, GitHub matching by key with an evidence rule, and fact-versus-inference synthesis. Load whenever researching a Jira ticket to assemble a brief an engineer can start from.
---

# Research, the engineering domain standard

What goes into a ready-to-start brief and how to gather it, so an engineer handed a ticket can begin work without hunting across apps. This is the standard the `ticket_research` command runs against. It contains no personal values. The repo allowlist, search scopes, and toggles come from the local `config.md`. The rules below are the agreed definition of a good brief, so a change here updates every research brief at once.

This is a *research* standard, not a classification standard. Where it touches issue status, it reuses the `ticket_standards` skill rather than redefining anything.

## What "ready to start" means

A brief is ready to start when it answers, in order: what the work is, what done looks like, what already exists, where the code is, what people have already said, what is still unknown, and where to begin. Concretely, gather enough to fill these components:

* Problem statement, taken from the ticket.
* Acceptance criteria. Use the ticket's own if present. If absent, synthesize a draft and flag it as synthesized, per the guess rule in the report_style skill.
* Prior art and context: linked Confluence docs and related issues.
* Code touchpoints: related GitHub PRs, branches, commits, and the files they touch.
* Relevant discussions: Slack threads about the ticket.
* Open questions and risks: unknowns, blockers, and ambiguities.
* Suggested starting point, clearly marked as a suggestion, not an instruction.

## Ticket resolution

The ticket is provided at run time as a Jira key (for example PROJ-123) or a full issue URL. Parse the key from either; if a `ticket_key_pattern` is set in config, use it to extract the key from a URL or pasted text. If no key can be extracted, stop and say so rather than guessing.

Follow issue links **one hop only**: is-blocked-by, blocks, relates-to, the parent epic, and subtasks. Do not walk the whole issue graph. One hop is enough to show prior art and dependencies without the brief ballooning.

For each related issue, label its status using the buckets in the `ticket_standards` skill (Done versus still active) so the reader sees active dependencies and finished prior art differently. Reference those buckets by pointer; do not restate them here.

## Confluence discovery

Find docs from the ticket's remote links, its "documentation" links, and page mentions on the ticket and on its parent epic, scoped to any `confluence_spaces` in config when set. If a linked doc is not accessible, note "linked doc not accessible" and move on rather than failing.

## GitHub matching

Search only the repos in the `github_repos` allowlist from config. Match by the ticket-key convention: the key appearing in a branch name, a PR title, or a commit message.

Apply the **evidence rule**: a key match is a confirmed link. A match on the feature name or title *without* the key is "possible, unconfirmed" and must be labelled that way. Never assert that an unverified PR belongs to the ticket.

If `github_repos` is empty, skip GitHub entirely and note that the allowlist was empty. Never fall back to searching all accessible repositories. Skip any allowlisted repo that is inaccessible or typo'd, with a one-line reason, rather than failing the run. Mark PRs found in archived repos as archived: history, not active work.

## Slack discovery

Search the channels in `slack_channels` from config for the ticket key and title. If the list is empty, skip Slack search and note it. For each hit, capture the thread link and a one-line gist, not the whole thread.

## Synthesis: fact versus inference

Keep facts and inferences distinct. Facts come straight from the ticket and the sources. Inferences are anything you construct, such as draft acceptance criteria, a suggested starting point, or a guessed link. Flag every inference, per the report_style skill. The reader must always be able to tell what the ticket said from what the brief inferred.

## Caps and clear state

A deep ticket can surface many related items, so length is controlled by item caps, not prose. Cap each evidence section to the top few items by relevance, honouring `code_context_cap` for code touchpoints, then a single overflow count, for example "+6 more PRs".

State the difference between *searched and found nothing* and *could not search*. An empty Slack result because nothing matched is not the same as a skipped search because no channels were configured or the connector was down. Record both plainly in the brief's Sources and gaps section.
