---
name: backtest-a-rule
description: Backtest a trading rule the user describes in words on QuanterLab and say plainly whether it beat simply holding the stock. Use when the user wants to test, check or backtest a strategy on a stock or ETF, asks "would this have worked", or names a rule such as an RSI level, a moving-average crossover or a breakout on a ticker.
---

# Backtest a rule

The user has an idea. Turn it into one clear rule, run it on QuanterLab, and tell them what happened in words they can act on.

## 1. Pin the rule down

Before running, four things have to be known. Ask only for what is missing, in one question:

- the ticker: a US stock or ETF, such as SPY or AAPL;
- the entry rule and the exit rule (an exit can be the opposite signal, a stop or a holding period);
- the history: ten years of daily bars unless the user says otherwise (the connector runs daily bars only);
- the direction: long only, unless the user asks for short or both.

Use `list_indicators` to map the user's words to indicator ids, settings and the columns they compute, for example RSI with a length of 14 computes `RSI_14`, and MACD computes `MACD_12_26_9` (the line), `MACDh_12_26_9` (the histogram) and `MACDs_12_26_9` (the signal line). Write each condition as `{"column": ..., "operator": ..., "value": ...}`. If the rule needs something the catalog does not have, say so instead of approximating it quietly.

## 2. Run it

Call `start_backtest` with the rule. Keep the defaults for position sizing and costs unless the user asks; the default charges a spread on every trade. Poll `get_backtest_progress` until it completes (usually under three minutes). Its last answer carries the result; if it does not, call `summarize_workflow` with the task id.

If the call is refused, read the refusal: it names what to change (a column the strategy does not compute lists the ones it does). Fix that once and run again; pass on any other refusal in its own words, without retrying in a loop.

## 3. Report it

Lead with the answer to the question people ask first: did the rule beat simply holding the same stock over the same years? The result says so, and for a run of a year or longer it carries a few rounded figures: the rule's return and holding's return, the Sharpe ratio, the worst drop and the number of trades. Put the two returns side by side. Then give:

- how the result reads (strong, promising, mixed or weak), and what that means in one sentence;
- one caution that fits this result: few trades, one stock, one period, or settings picked by eye;
- the link to the full report on QuanterLab (`dashboard_url`), where the trades and charts are.

Quote the figures as they come; they are rounded on purpose. Never add precision and never invent a figure that is missing: a run shorter than a year carries none, and the exact figures are in the report.

If the result says the rule never traded, say exactly that: the run tells nothing about the rule yet. The usual cause is an entry that never matched; offer to loosen it or check it, rather than reading the flat result as a verdict on the idea.

## 4. Offer one next step

One, not a menu. If it looked good, offer a walk-forward (the walk-forward-check skill) to see whether it holds on years it was not tuned on. If holding did better, offer to try the rule on a stock that swings more, or with a looser entry.

A result describes how a rule behaved on past data. It is not advice to trade.
