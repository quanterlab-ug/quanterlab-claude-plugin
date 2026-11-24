---
name: run-a-saved-strategy
description: Run a strategy the user built and saved on the QuanterLab canvas and report how it did. Use when the user mentions their saved QuanterLab projects, canvas strategies or circuits, or asks to run, rerun or check one of them.
---

# Run a saved strategy

QuanterLab reports how a strategy behaved on past market data. It is not investment advice, it places no orders, and a paper trade on QuanterLab is simulated.

## The connector

This skill works through QuanterLab's connector at `https://quanterlab.com/mcp`. When none of its tools is available in the conversation (no `get_started`, no `search`, no `start_test`), the connector is not connected yet, and there is no QuanterLab result to report. It is connected once: in Claude, from this plugin's page (Connect on the QuanterLab connector, then sign in); in Claude Code, with `/mcp`, then quanterlab.

`get_started` returns an overview of QuanterLab and three setups from its Library to choose from, for a request that names no saved strategy.

## The run

1. `list_my_primitives_projects` lists the user's saved projects, newest first. A project the user named is matched by its name. When the user named none, the names are the choices to ask about; the newest is the one to run only when the user says so.
2. `start_primitives_run` takes that project's id and, as `question`, one sentence restating what the run should answer (not the user's message): it titles the report and is kept only as that. The strategy runs as saved, at the date saved with it; it takes no settings. The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url`. A longer run answers that it is still going, with a task id for `get_primitives_run_progress`; each progress call waits for the run on QuanterLab's side, and a new call is useful only after one that says the run is still going.
3. Where the host shows cards, the run has its own card: the project's circuit, drawn as the lab draws it, lights up card by card as it runs, and the result appears under it. `show_report_card` is not needed for such a run. Without a card, `report_url` holds the run written up as a report with its curves, which opens without a login.
4. In `result`, each branch of the project comes under its card name in `figures.branches`, with its dates, whether it beat simply holding what it trades over the same dates, its benchmark, and which branch ended ahead. A branch tested over a year or longer also carries its rounded return, the holding's return, Sharpe, worst drop and trades. A branch tested over less than a year carries no figures.
5. `get_primitives_run_result` adds each result's tiers and any look-ahead warning.
6. The card offers its next steps as choices the user can tap; without a card, `next_steps` in the result lists them. A lab run's next steps are changes made on the canvas, which `start_primitives_run` cannot pass: `result.lab_url` opens the project, where the user makes the change and saves it, and `start_primitives_run` then runs it again.

`show_report_card` shows a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes.

A refused run's message says why it was refused.
