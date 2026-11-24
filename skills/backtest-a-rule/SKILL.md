---
name: backtest-a-rule
description: Backtest a trading rule the user describes in words on QuanterLab and compare it with simply holding the same stock. Use when the user wants to test, check or backtest a strategy on a stock or ETF, asks "would this have worked", or names a rule such as an RSI level, a moving-average crossover or a breakout on a ticker.
---

# Backtest a rule

QuanterLab reports how a rule behaved on past market data. It is not investment advice, it places no orders, and a paper trade on QuanterLab is simulated.

## The connector

This skill works through QuanterLab's connector at `https://quanterlab.com/mcp`. When none of its tools is available in the conversation (no `get_started`, no `search`, no `start_test`), the connector is not connected yet, and there is no QuanterLab result to report. It is connected once: in Claude, from this plugin's page (Connect on the QuanterLab connector, then sign in); in Claude Code, with `/mcp`, then quanterlab.

## 1. The rule and its defaults

`get_started` returns an overview of QuanterLab and three setups from its Library to choose from; a chosen setup plays from its stored result and uses none of the account's runs. It suits a request that names no stock and no rule.

A rule has a ticker, an entry, an exit, a span of history, costs and a direction. When the request leaves one of them open, these are the defaults, and the result names the ones it used:

- the ticker: a US stock or ETF, such as SPY or AAPL; SPY for a rule named without a stock;
- the entry rule and the exit rule (an exit can be the opposite signal, a stop or a holding period);
- the history: a span back from today, `2y`, `5y`, `10y` or `max` (the whole history held), never a start date; `10y` when none is named. "Since 2010" is `10y` or `max`, and the result says which years it covered. The connector runs daily bars only;
- the costs: a spread charged on every trade;
- the direction: long only, unless the user asks for short or both.

For example, an RSI rule named without a stock runs as: SPY, the last 10 years, buy when RSI(2) is below 10, sell when it is above 70, costs 0.1% a trade.

QuanterLab's ready tests on a stock or fund (mean reversion, trend following, momentum, buying the dip) run with `start_test`; the test's card draws its circuit and lights each card as it runs.

A filter that must also hold on the day the entry fires goes in the confirmation slot. The common one is a trend filter: a two-day RSI pullback that buys only while the close is above its 100-day average is `confirmation_indicator` `{"id": "sma", "params": {"length": 100}}` and `confirmation_conditions` `[{"column": "close", "operator": ">", "value": "SMA_100"}]`.

`list_indicators` maps the user's words to indicator ids, settings and the columns they compute: RSI with a length of 14 computes `RSI_14`, and MACD computes `MACD_12_26_9` (the line), `MACDh_12_26_9` (the histogram) and `MACDs_12_26_9` (the signal line). Each condition is written `{"column": ..., "operator": ..., "value": ...}`. A rule that needs something the catalog does not have cannot be expressed exactly; an approximation of it tests a different rule.

## 2. The run

`start_backtest` takes the rule and, as `question`, one sentence restating what the run should answer (not the user's message): it titles the report and is kept only as that.

The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url`. A longer run answers that it is still going, with a task id for `get_backtest_progress`; each progress call waits for the run on QuanterLab's side, and a new call is useful only after one that says the run is still going.

Runs count against the account's daily allowance. `get_started` and each run's answer give today's count, a refused call does not count, and `list_my_running_tasks` gives `runs_left_today`. With none left, a new run is refused until the allowance renews.

A refusal names what to change: a column the strategy does not compute comes back with the list of the ones it does. Other refusals carry their reason in their own words, and the same call sent again unchanged gets the same refusal.

## 3. The result

Where the host shows cards, the run has its own card: it follows the run and fills in by itself with the verdict, the figures, the chart, the report link and the next steps. `show_report_card` is not needed for such a run.

Without a card, the result carries the same answer:

- the rule against simply holding the same stock over the same years (and SPY when that differs). For a run of a year or longer, `result.figures` carries the rule's return and holding's return, the Sharpe ratio, the worst drop and the number of trades;
- `report_url`, the run written up as a QuanterLab report, which opens without a login;
- `next_steps`.

`show_report_card` shows a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes.

A finished run without `report_url` (its report could not be made) has its answer in `result`: its headline, its figures and its next steps. It has no card.

The figures come rounded on purpose. A run shorter than a year carries none.

A result that says the rule never traded tells nothing about the rule yet. The usual cause is an entry that never matched, and loosening or checking the entry is the way to learn more.

A single backtest has limits worth weighing: few trades, one stock, one period, and settings picked by looking at the same history.

## 4. Next steps

The card offers its next steps as choices the user can tap; without a card, `next_steps` in the result lists them. Common ones: the same rule as a walk-forward (the walk-forward-check skill), the rule on another stock or period, or one setting changed.
