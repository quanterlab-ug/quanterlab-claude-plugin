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

Use `list_indicators` to map the user's words to indicator ids and settings, for example RSI with a length of 14. If the rule needs something the catalog does not have, say so instead of approximating it quietly.

## 2. Run it

Call `start_backtest` with the rule. Keep the defaults for position sizing and costs unless the user asks; the default charges a spread on every trade. Poll `get_backtest_progress` until it completes (usually under three minutes), then call `summarize_workflow` with the task id.

If the call is refused, pass the refusal on in its own words. Do not retry in a loop and do not guess the reason.

## 3. Report it

Lead with the answer to the question people ask first: did the rule beat simply holding the same stock over the same years? The summary says so. Then give:

- how the result reads (strong, promising, mixed or weak), and what that means in one sentence;
- one caution that fits this result: few trades, one stock, one period, or settings picked by eye;
- the link to the full report on QuanterLab (`dashboard_url`), where the trades and charts are.

QuanterLab returns this result in words, not figures. Never invent a return, a Sharpe ratio or a drawdown. If the user wants the numbers, they are in the report.

## 4. Offer one next step

One, not a menu. If it looked good, offer a walk-forward (the walk-forward-check skill) to see whether it holds on years it was not tuned on. If holding did better, offer to try the rule on a stock that swings more, or with a looser entry.

A result describes how a rule behaved on past data. It is not advice to trade.
