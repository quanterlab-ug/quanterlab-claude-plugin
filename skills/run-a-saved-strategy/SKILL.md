---
name: run-a-saved-strategy
description: Run a strategy the user built and saved on the QuanterLab canvas and report how it did. Use when the user mentions their saved QuanterLab projects, canvas strategies or circuits, or asks to run, rerun or check one of them.
---

# Run a saved strategy

1. Call `list_my_primitives_projects`. It lists the user's saved projects, newest first. If the user named one, match it by name. If not, show the names as choices and ask which one; take the newest only if the user said so.
2. Call `start_primitives_run` with that project's id, and pass the user's own words as `question`: the report is titled by it. The strategy runs as saved, at the date saved with it; it takes no settings. The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url` at once. A longer run answers that it is still going; then call `get_primitives_run_progress` with the task id. Each call waits for the run on QuanterLab's side, so call it again only when it answers that the run is still going.
3. Where the host shows cards, the run shows in its own card: the project's circuit, drawn as the lab draws it, lights up card by card as it runs, and the result appears under it. Do not call `show_report_card` for it, and do not repeat what it shows. While it runs, say in one sentence what is running; never ask the user to come back, to wait or to check later. Where no card shows, give `report_url`: the run written up as a report, with its curves, which opens without a login.
4. Report from `result`, in one sentence where the card shows. Each branch of the project comes under its card name in `figures.branches`, with its dates, whether it beat simply holding what it trades over the same dates, its benchmark, and which branch ended ahead. A branch tested over a year or longer also carries its rounded return, the holding's return, Sharpe, worst drop and trades: quote them as given. A branch tested over less than a year carries no figures; say so instead of estimating them.
5. `get_primitives_run_result` adds each result's tiers and any look-ahead warning, when the user asks for more.
6. Where the card shows, it offers its next steps as choices the user can tap: ask which one they want, without listing them again. Where no card shows, offer two or three next steps to choose from, and ask which one they want. A lab run's next steps are changes made on the canvas, which `start_primitives_run` cannot pass: give `result.lab_url`, where the user opens the project, makes the change and saves it, then run it again with `start_primitives_run`.

`show_report_card` is for a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes: call it once with that link.

If a run is refused, pass the message on as written, without guessing the reason.
