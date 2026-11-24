---
name: scan-for-candidates
description: Scan an index on QuanterLab for stocks that tend to return to their own average, or for pairs of stocks that move together. Use when the user asks for mean-reverting stocks, pairs trading candidates, cointegrated pairs, or stocks to try a mean-reversion model on.
---

# Scan for candidates

QuanterLab reports how stocks behaved on past market data. A scan's list is not a recommendation to buy or sell anything, QuanterLab places no orders, and a paper trade on QuanterLab is simulated.

## The connector

This skill works through QuanterLab's connector at `https://quanterlab.com/mcp`. When none of its tools is available in the conversation (no `get_started`, no `search`, no `start_test`), the connector is not connected yet, and there is no QuanterLab result to report. It is connected once: in Claude, from this plugin's page (Connect on the QuanterLab connector, then sign in); in Claude Code, with `/mcp`, then quanterlab.

`get_started` returns an overview of QuanterLab and three setups from its Library to choose from, for a request that names nothing to scan for.

## 1. The scan and its defaults

- Single stocks that return to their own average: `start_mr_scan`.
- Pairs of stocks that move together: `start_pairs_scan`.

When the request leaves a setting open, these are the defaults, and the result names them: the S&P 500 when no index is named (the widest; the NASDAQ 100 and the Dow 30 are narrower and quicker), three years of daily history, and each filter's own defaults. Each scan takes, as `question`, one sentence restating what it should answer (not the user's message): it titles the report and is kept only as that.

Each filter is `{"type": ..., "params": {...}}`, with the scanner's own setting names. For single stocks: `hurst` (`max_h`, default 0.45; `window`), `halflife` (`min_hl`, `max_hl` in days, default 5 to 100), `adf` (`max_pvalue`, default 0.05), `kpss` (`min_pvalue`, default 0.05), `variance_ratio`, `autocorr`, `volatility` and `liquidity` (`min_dv`, average daily dollar volume in millions). For pairs: `correlation` (`min_corr`), `cointegration` (`max_pvalue`), `spread_halflife`, `beta_stability`, `spread_hurst`, `johansen`. A setting left out keeps its default; a name the scanner does not know is refused with the filter's settings.

## 2. The result

The start waits a little for the scan: a scan that finishes inside it answers with `result` and `report_url`. A longer one answers that it is still going, with a task id for the matching progress tool; each progress call waits for the scan on QuanterLab's side, and a new call is useful only after one that says the scan is still going. The result's `candidates` block holds:

- `filters`: each filter's rule as the scanner applied it, and how many names passed it out of how many that reached it;
- `names`: up to ten, the ones that passed every filter first, then the closest misses, ranked, each with its own statistics rounded and, for a miss, the filter that stopped it.

Where the host shows cards, the scan has its own card, which fills in by itself; `show_report_card` is not needed for it. Without a card, `report_url` holds the funnel and the names that passed, in a report that opens without a login.

Only the names in the list passed or nearly passed; the statistics come rounded on purpose. When none or few passed, the closest misses are misses, and the filter that stopped most of them is the one to loosen.

A rerun with looser filters is looser only where its `filters` rules differ from the earlier ones.

## 3. Testing a candidate

A scan finds candidates; only a test shows whether trading one would have worked. The card offers its next steps as choices the user can tap, such as testing the first name that passed; without a card, `next_steps` in the result lists them. `start_stochastic_backtest` tests the candidate the user picks, for example with an Ornstein-Uhlenbeck mean-reversion model (`list_stochastic_methods` lists the models and their settings).

A grid of settings, a sweep or "the best setting" of that model is one `start_stochastic_backtest` call with `sweep`: one task and one run of the account's allowance, and its answer carries the grid and the best setting's backtest.

That test has its own card, set against holding the same stock, as the backtest-a-rule skill describes.
