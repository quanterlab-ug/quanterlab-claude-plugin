---
name: backtest-a-rule
description: Backtest a trading rule the user describes in words on QuanterLab and say plainly whether it beat simply holding the stock. Use when the user wants to test, check or backtest a strategy on a stock or ETF, asks "would this have worked", or names a rule such as an RSI level, a moving-average crossover or a breakout on a ticker.
---

# Backtest a rule

The user has an idea. Turn it into one clear rule, run it on QuanterLab straight away, and tell them what happened in words they can act on.

## 1. Run first

Ask nothing before the first run. What the request leaves open gets a sensible default:

- the ticker: a US stock or ETF, such as SPY or AAPL; SPY when none is named;
- the entry rule and the exit rule (an exit can be the opposite signal, a stop or a holding period);
- the history: a span back from today, `2y`, `5y`, `10y` or `max` (the whole history held), never a start date; `10y` when none is named. For "since 2010", use `10y` or `max` and say which years the run covered. The connector runs daily bars only;
- the costs: the default charges a spread on every trade;
- the direction: long only, unless the user asks for short or both.

Run with sensible defaults and name them in one line with the result, for example: SPY, the last 10 years, buy when RSI(2) is below 10, sell when it is above 70, costs 0.1% a trade. The user's own changes come after the result, as next steps. Ask first only when the request names nothing to run, no rule and no stock: then call `get_started`, which offers ready tests to run with one tap.

One of QuanterLab's ready tests on a stock or fund (mean reversion, trend following, momentum, buying the dip, the 200-day line) runs with `start_test`, straight away: its card draws the test's circuit and lights each card as it runs.

A filter that must also hold on the day the entry fires goes in the confirmation slot. The common one is a trend filter, as in Larry Connors' two-day RSI pullback, which buys only while the close is above its 200-day average: `confirmation_indicator` `{"id": "sma", "params": {"length": 200}}` and `confirmation_conditions` `[{"column": "close", "operator": ">", "value": "SMA_200"}]`.

Use `list_indicators` to map the user's words to indicator ids, settings and the columns they compute, for example RSI with a length of 14 computes `RSI_14`, and MACD computes `MACD_12_26_9` (the line), `MACDh_12_26_9` (the histogram) and `MACDs_12_26_9` (the signal line). Write each condition as `{"column": ..., "operator": ..., "value": ...}`. If the rule needs something the catalog does not have, say so instead of approximating it quietly.

## 2. Run it

Call `start_backtest` with the rule, and pass the user's own words as `question`: the report is titled by it. Say in one line that you used the backtester and why.

The call waits a little for the run: a run that finishes inside it answers with `result` and `report_url` at once. A longer run answers that it is still going; then call `get_backtest_progress` with the task id. Each call waits for the run on QuanterLab's side, so call it again only when it answers that the run is still going.

Every account has free runs every day; a refused call does not count, and `get_started` and every run's answer give today's count. With none of today's runs left (`list_my_running_tasks` gives `runs_left_today`, or a refusal says so), say when they come back instead of trying again. If the call is refused, read the refusal: it names what to change (a column the strategy does not compute lists the ones it does). Fix that once and run again; pass on any other refusal in its own words, without retrying in a loop.

## 3. Show it

Where the host shows cards, the run shows in its own card: it follows the run and fills in by itself with the verdict, the figures, the chart, the report link and the next steps. Do not call `show_report_card` for it, and do not repeat what it shows. While it runs, say in one sentence what is running; never ask the user to come back, to wait or to check later. When the result is in, add one sentence on what it means and one caution that fits it (few trades, one stock, one period, or settings picked by eye).

Where no card shows, answer in three parts:

- the result in one sentence against simply holding the same stock over the same years (and SPY when that differs). For a run of a year or longer, `result.figures` carries the rule's return and holding's return, the Sharpe ratio, the worst drop and the number of trades: put the two returns side by side;
- `report_url`, the run written up as a QuanterLab report (it opens without a login);
- the next steps below.

`show_report_card` is for a report link that `summarize_workflow` or `save_workflow_result` answers with, or one the user pastes: call it once with that link.

A finished run without `report_url` (its report could not be made) has its answer in `result`: give its headline, the figures that matter and its next steps. It has no card.

Quote the figures as they come; they are rounded on purpose. Never add precision and never invent a figure that is missing: a run shorter than a year carries none.

If the result says the rule never traded, say exactly that: the run tells nothing about the rule yet. The usual cause is an entry that never matched; offer to loosen it or check it, rather than reading the flat result as a verdict on the idea.

## 4. Offer the next steps

Where the card shows, it offers its next steps as choices the user can tap: ask which one they want, without listing them again. Where no card shows, offer two or three concrete next steps to choose from, and ask which one they want. Take them from the card's or the result's `next_steps`, or from these: the same rule as a walk-forward (the walk-forward-check skill), the rule on another stock or period, or one setting changed.

A result describes how a rule behaved on past data. It is not advice to trade.
