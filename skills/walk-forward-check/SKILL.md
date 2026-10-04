---
name: walk-forward-check
description: Check whether a trading rule's result is real or fitted to its own history with a walk-forward on QuanterLab. Use when the user asks whether a backtest is overfit, robust, luck or skill, whether it would hold out of sample, or right after a backtest that looked good.
---

# Walk-forward check

A backtest whose settings were picked by looking at the same history will look better than it ever trades. A walk-forward removes that advantage: it tunes the settings on one window, trades them on the next window it has not seen, steps forward and repeats. Only the unseen windows count.

## 1. Set it up

- Start from the user's rule as the template, in the same shape as a backtest, with conditions written `{"column": ..., "operator": ..., "value": ...}`.
- Pick the two settings the user would actually tune, for example the RSI length from 7 to 21 and the buy level from 20 to 40. If it is not clear which two matter, ask.
- Defaults: five years of daily bars, tune on about a year (252 sessions), test on the next quarter (63 sessions), three steps per setting. Keep the grid small; a large grid finds noise.
- A rule that trades rarely, such as a moving-average or MACD crossover, needs long windows: shorter ones leave most test windows without a single trade, and the test then says little.

## 2. Run it

Call `start_walkforward` and poll `get_walkforward_progress`. Its last answer carries the result; if it does not, call `summarize_workflow` with the task id. If the engine refuses the size of the job, use fewer steps per setting or a shorter history, as its message says.

## 3. Report it

- Answer first: did it hold up on the years it never saw?
- When the result carries figures, use them: the return and Sharpe ratio on those windows against the Sharpe ratio when tuned, and holding the stock over the same stretch. The result also says which did better over those years, the rule or simply holding; say it.
- Say how many windows made money and how many did not trade at all. When most did not trade, say the test says little, and suggest longer windows or a rule that trades more often. When the result says it never traded at all, the run says nothing about the rule yet: check the rule with a plain backtest first.
- If it did not hold up, say so plainly: the tuned result was probably fitted to its own history. That is a useful answer, not a failure of the tool.
- Give the link to the full report (`dashboard_url`) for the window-by-window detail.

Quote the figures as they come; never add precision and never quote a number the tool did not return.

## 4. Count the tries

Every variation the user has tried in this conversation is a trial. When there have been many, say so: the best of many tries looks better than it is. Suggest settling the rule before the next run rather than tuning it further.
