---
name: scan-for-candidates
description: Find stocks that tend to snap back to their average, or pairs of stocks that move together, by scanning an index on QuanterLab, then test one of them. Use when the user asks for mean-reverting stocks, pairs trading candidates, cointegrated pairs, or stocks to try a mean-reversion model on.
---

# Scan for candidates

## 1. Choose the scan

- Single stocks that return to their own average: `start_mr_scan`.
- Pairs of stocks that move together: `start_pairs_scan`.

If the user did not name an index, ask: the S&P 500 is the widest, the NASDAQ 100 and the Dow 30 are narrower. Use three years of daily history unless the user says otherwise.

Each filter is `{"type": ..., "params": {...}}`, with the scanner's own setting names. For single stocks: `hurst` (`max_h`, default 0.45; `window`), `halflife` (`min_hl`, `max_hl` in days, default 5 to 100), `adf` (`max_pvalue`, default 0.05), `kpss` (`min_pvalue`, default 0.05), `variance_ratio`, `autocorr`, `volatility` and `liquidity` (`min_dv`, average daily dollar volume in millions). For pairs: `correlation` (`min_corr`), `cointegration` (`max_pvalue`), `spread_halflife`, `beta_stability`, `spread_hurst`, `johansen`. Leave a setting out to keep its default; a name the scanner does not know is refused with the filter's settings.

## 2. Run it and read it

Poll the matching progress tool until it finishes; its last answer carries the result (or call `summarize_workflow` with the task id). The result's `candidates` block holds:

- `filters`: each filter's rule as the scanner applied it, and how many names passed it out of how many that reached it;
- `names`: up to ten, the ones that passed every filter first, then the closest misses, ranked, each with its own statistics rounded and, for a miss, the filter that stopped it.

Name the stocks that passed. When none or few passed, name the closest misses as misses, say which filter stopped most of them, and offer to loosen that filter. Quote the statistics as they come, never add precision, and never name a stock that is not in the list: the full table is in the report behind `dashboard_url`.

When the user asks to loosen the filters and scan again, compare the new `filters` rules with the old ones before saying the rerun was looser.

## 3. Test one

A scan finds candidates; only a test shows whether trading one would have worked. Offer to test the candidate the user picks with `start_stochastic_backtest`, for example an Ornstein-Uhlenbeck mean-reversion model (`list_stochastic_methods` lists the models and their settings). Report that test the way the backtest-a-rule skill does: the answer first, one caution, the link.
