# executive_ops

The executive operations plugin. It holds the `board_metrics` standard, defining the monthly metric catalog, key-result health by pace to target, month-over-month trend, and the needs-board-attention lens, and the `board_pack` command that pulls those metrics from Looker, NetSuite, and Jira, assembles the pack, and posts the summary to a private leadership channel. It reuses `report_style` from house_standards, so the board pack reads in the same voice as the engineering reports.

## What it ships

* **Skill: board_metrics** — the executive domain standard. The metric catalog (financial from NetSuite, growth and product from Looker), key-result health (achieved / on track / at risk / off track / not started) by pace to target, month-over-month trend, the escalation lens, and the source-query patterns. Loaded automatically when a board pack or executive summary is being prepared.
* **Command: `/executive_ops:board_pack`** — the monthly board metrics pack. Reads Looker, NetSuite, and Jira, computes metrics, key-result health, and month-over-month trend (degrading to "no prior month to compare" on a first run), writes the pack to output, and posts the summary and board-attention pointer to the board channel.

## At a glance

* Cadence: monthly, after month-end close, same day each month
* Connectors: Looker, NetSuite, Atlassian (Jira), Slack. See connectors.md.
* Reuses: report_style from house_standards
* Reads config: the executive section of the local config.md (it does not need the engineering teams or status mapping)
* Writes to: output/executive_ops/board_pack/

## Run it

1. Install house_standards and executive_ops from the marketplace.
2. Authorize the connectors in Cowork. Looker and NetSuite are not in the default connector set and must be added first; depending on your workspace they may need a custom or MCP-based connector. Atlassian and Slack are already used by engineering_ops. See connectors.md.
3. Copy config/config.example.md to a `config.md` in your Cowork working folder and fill in the executive section: look ids, NetSuite report refs, the OKR project and fields, targets and plan, fiscal year start, runway floor, and the board channel.
4. Run `/executive_ops:board_pack` by hand once. The first run usually reveals a look id, NetSuite report name, or Jira field that does not match config. Fix config and rerun.
5. When the metrics and OKR health look right, schedule it with `/schedule` for a fixed day each month after close.

## Notes

* Trend appears only from the second run on. It compares this month to the previous run's saved output at output/executive_ops/board_pack/latest.md. The first run says "no prior month to compare." Run it monthly, on the same day, so the windows tile.
* OKRs are quarterly; the monthly pack shows quarter-to-date progress. The last pack of a quarter is the one that scores the quarter.
* Connectors degrade gracefully: if one source is down, its section is marked unavailable and flagged at the top. If all three data sources are down, the command stops and writes nothing over a good prior pack.
* The pack holds sensitive financial and OKR data. It distributes only to the configured board channel.
* Plugin support is a research preview and the connector format moves; the Looker and NetSuite wiring is the least settled part. Verify the manifests against the current Cowork plugin docs before relying on them.
