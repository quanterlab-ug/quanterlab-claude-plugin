---
name: run-a-saved-strategy
description: Run a strategy the user built and saved on the QuanterLab canvas and report how it did. Use when the user mentions their saved QuanterLab projects, canvas strategies or circuits, or asks to run, rerun or check one of them.
---

# Run a saved strategy

1. Call `list_my_primitives_projects`. It lists the user's saved projects, newest first. If the user named one, match it by name; if not, show the names and ask which one, or take the newest if the user said so.
2. Call `start_primitives_run` with that project's id. The strategy runs at the date saved with it. Poll `get_primitives_run_progress`; when the run has finished, its answer carries `result`.
3. Report from `result`. Each branch of the project comes under its card name in `figures.branches`, with its dates, whether it beat simply holding what it trades over the same dates, its benchmark, and which branch ended ahead. A branch tested over a year or longer also carries its rounded return, the holding's return, Sharpe, worst drop and trades: quote them as given. A branch tested over less than a year carries no figures; say so instead of estimating them.
4. `get_primitives_run_result` adds each result's tiers and any look-ahead warning, when the user asks for more.
5. Say where the full picture is: the QuanterLab canvas shows every card's output for the run.

If a run is refused, pass the message on as written, without guessing the reason.
