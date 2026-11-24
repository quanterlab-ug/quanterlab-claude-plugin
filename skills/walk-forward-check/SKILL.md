---
name: walk-forward-check
description: Check whether a trading rule's result holds on years it was not tuned on, with a walk-forward on QuanterLab. Use when the user asks whether a backtest is overfit, robust, luck or skill, or whether it would hold out of sample.
---

# Walk-forward check

QuanterLab reports how a rule behaved on past market data. It is not investment advice, it places no orders, and a paper trade on QuanterLab is simulated.

## The connector

This skill works through QuanterLab's connector at `https://quanterlab.com/mcp`. When none of its tools is available in the conversation (no `get_started`, no `search`, no `start_test`), the connector is not connected yet, and there is no QuanterLab result to report. It is connected once: in Claude, from this plugin's page (Connect on the QuanterLab connector, then sign in); in Claude Code, with `/mcp`, then quanterlab.

A backtest whose settings were picked by looking at the same history looks better than it trades. A walk-forward removes that advantage: it tunes the settings on one window, trades them on the next window it has not seen, steps forward and repeats. Only the unseen windows count.

## 1. The rule and its defaults

A walk-forward needs a rule. `get_started` returns an overview of QuanterLab and three setups from its Library to choose from, for a request that names no rule.

- The user's rule is the template, in the same shape as a backtest, with conditions written `{"column": ..., "operator": ..., "value": ...}`. "An RSI rule" alone has a common form: buy when RSI falls below a level, sell when it rises above 70.
- The two settings the rule depends on most are the ones tuned, for example the RSI length from 7 to 21 and the buy level from 20 to 40.
- The defaults, named in the result: five years of daily bars, or the span the user named; tuning on about a year (252 sessions), testing on the next quarter (63 sessions), three steps per setting. A small grid matters: a large grid finds noise.
- A rule that trades rarely, such as a moving-average or MACD crossover, needs long windows: shorter ones leave most test windows without a single trade, and the test then says little.

## 2. The run

`start_walkforward` takes the rule and, as `question`, one sentence restating what the run should answer (not the user's message): it titles the report and is kept only as that. The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url`. A longer run answers that it is still going, with a task id for `get_walkforward_progress`; each progress call waits for the run on QuanterLab's side, and a new call is useful only after one that says the run is still going.

A job bigger than one run does in full comes back with the nearest versions that fit, each one tap.

## 3. The result

Where the host shows cards, the run has its own card: it follows the run and fills in by itself with the verdict, the figures, the windows tuned and tested, the chart against holding, the report link and the next steps. `show_report_card` is not needed for such a run.

Without a card, the result carries the same answer, with `report_url` (it opens without a login and has the window-by-window detail):

- whether the rule held up on the years it never saw;
- when the result carries figures: the return and Sharpe ratio on those windows against the Sharpe ratio when tuned, and holding the stock over the same stretch, with which did better over those years, the rule or simply holding;
- how many windows made money and how many did not trade at all. When most did not trade, the test says little, and longer windows or a rule that trades more often say more. A result that never traded at all says nothing about the rule yet; a plain backtest of the rule comes first;
- a rule that did not hold up was probably fitted to its own history, which is a useful answer.

`show_report_card` shows a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes.

The figures come rounded on purpose, and only the figures the tool returns exist.

## 4. Next steps

The card offers its next steps as choices the user can tap; without a card, `next_steps` in the result lists them, such as another stock or period, or fewer settings tuned.

## 5. The number of tries

Every variation tried in a conversation is a trial, and the best of many tries looks better than it is. A rule settled before the next run says more than one tuned further.
