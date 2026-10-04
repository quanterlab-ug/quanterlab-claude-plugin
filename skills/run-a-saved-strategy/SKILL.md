---
name: run-a-saved-strategy
description: Run a strategy the user built and saved on the QuanterLab canvas and report how it did. Use when the user mentions their saved QuanterLab projects, canvas strategies or circuits, or asks to run, rerun or check one of them.
---

# Run a saved strategy

1. Call `list_my_primitives_projects`. It lists the user's saved projects, newest first. If the user named one, match it by name; if not, show the names and ask which one, or take the newest if the user said so.
2. Call `start_primitives_run` with that project's id. The strategy runs at the date saved with it. Poll `get_primitives_run_progress`, then read `get_primitives_run_result`.
3. Report in words: how it did against its benchmark, its drawdown band, how many trades it made, and any look-ahead warning the result carries. The result comes in tiers and bands, not figures; never turn a band into a number.
4. Say where the full picture is: the QuanterLab canvas shows every card's output for the run.

If a run is refused, pass the message on as written, without guessing the reason.
