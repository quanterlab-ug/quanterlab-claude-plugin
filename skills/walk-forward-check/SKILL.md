---
name: walk-forward-check
description: Check whether a trading rule's result is real or fitted to its own history with a walk-forward on QuanterLab. Use when the user asks whether a backtest is overfit, robust, luck or skill, whether it would hold out of sample, or right after a backtest that looked good.
---

# Walk-forward check

A backtest whose settings were picked by looking at the same history will look better than it ever trades. A walk-forward removes that advantage: it tunes the settings on one window, trades them on the next window it has not seen, steps forward and repeats. Only the unseen windows count.

## 1. Set it up

- Start from the user's rule as the template, in the same shape as a backtest.
- Pick the two settings the user would actually tune, for example the RSI length from 7 to 21 and the buy level from 20 to 40. If it is not clear which two matter, ask.
- Defaults: five years of daily bars, tune on about a year (252 sessions), test on the next quarter (63 sessions), three steps per setting. Keep the grid small; a large grid finds noise.

## 2. Run it

Call `start_walkforward`, poll `get_walkforward_progress`, then call `summarize_workflow` with the task id. If the engine refuses the size of the job, use fewer steps per setting or a shorter history, as its message says.

## 3. Report it

- Answer first: did it hold up on the years it never saw?
- Say in one sentence why that matters more than the tuned backtest.
- If it did not hold up, say so plainly: the tuned result was probably fitted to its own history. That is a useful answer, not a failure of the tool.
- Give the link to the full report (`dashboard_url`) for the window-by-window detail.

Never quote a number the tool did not return.

## 4. Count the tries

Every variation the user has tried in this conversation is a trial. When there have been many, say so: the best of many tries looks better than it is. Suggest settling the rule before the next run rather than tuning it further.
