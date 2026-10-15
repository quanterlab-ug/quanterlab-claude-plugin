---
name: walk-forward-check
description: Check whether a trading rule's result is real or fitted to its own history with a walk-forward on QuanterLab. Use when the user asks whether a backtest is overfit, robust, luck or skill, whether it would hold out of sample, or right after a backtest that looked good.
---

# Walk-forward check

A backtest whose settings were picked by looking at the same history will look better than it ever trades. A walk-forward removes that advantage: it tunes the settings on one window, trades them on the next window it has not seen, steps forward and repeats. Only the unseen windows count.

## 1. Run first

- Ask nothing before the first run. Run with sensible defaults and name them in one line with the result; the user's own changes come after the result, as next steps.
- Start from the user's rule as the template, in the same shape as a backtest, with conditions written `{"column": ..., "operator": ..., "value": ...}`. When they named only "an RSI rule" or similar, use the common form: buy when RSI falls below a level, sell when it rises above 70.
- Tune the two settings the rule depends on most, for example the RSI length from 7 to 21 and the buy level from 20 to 40.
- The defaults: five years of daily bars, or the span the user named; tune on about a year (252 sessions), test on the next quarter (63 sessions), three steps per setting. Keep the grid small; a large grid finds noise.
- A rule that trades rarely, such as a moving-average or MACD crossover, needs long windows: shorter ones leave most test windows without a single trade, and the test then says little.

## 2. Run it

Call `start_walkforward`, and pass the user's own words as `question`: the report is titled by it. The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url` at once. A longer run answers that it is still going; then call `get_walkforward_progress` with the task id. Each call waits for the run on QuanterLab's side, so call it again only when it answers that the run is still going.

If QuanterLab answers that the job is bigger than one run does in full, it offers the nearest versions that fit: offer those, one tap each, rather than shrinking the job yourself.

## 3. Show it

Where the host shows cards, the run shows in its own card: it follows the run and fills in by itself with the verdict, the figures, the windows tuned and tested, the chart against holding, the report link and the next steps. Do not call `show_report_card` for it, and do not repeat what it shows. While it runs, say in one sentence what is running; never ask the user to come back, to wait or to check later. When the result is in, add one sentence: did it hold up on the years it never saw. Add one caution when it fits, such as most windows not trading.

Where no card shows, give `report_url` with the answer (it opens without a login and has the window-by-window detail), and:

- answer first: did it hold up on the years it never saw?
- when the result carries figures, use them: the return and Sharpe ratio on those windows against the Sharpe ratio when tuned, and holding the stock over the same stretch. The result also says which did better over those years, the rule or simply holding; say it;
- say how many windows made money and how many did not trade at all. When most did not trade, the test says little: suggest longer windows or a rule that trades more often. When the result says it never traded at all, the run says nothing about the rule yet: check the rule with a plain backtest first;
- if it did not hold up, say so plainly: the tuned result was probably fitted to its own history. That is a useful answer.

`show_report_card` is for a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes: call it once with that link.

Quote the figures as they come; never add precision and never quote a number the tool did not return.

## 4. Offer the next steps

Where the card shows, it offers its next steps as choices the user can tap: ask which one they want, without listing them again. Where no card shows, offer two or three concrete next steps to choose from (the card's or the result's `next_steps`, another stock or period, fewer settings tuned), and ask which one they want.

## 5. Count the tries

Every variation the user has tried in this conversation is a trial. When there have been many, say so: the best of many tries looks better than it is. Suggest settling the rule before the next run rather than tuning it further.
