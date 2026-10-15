---
name: scan-for-candidates
description: Find stocks that tend to snap back to their average, or pairs of stocks that move together, by scanning an index on QuanterLab, then test one of them. Use when the user asks for mean-reverting stocks, pairs trading candidates, cointegrated pairs, or stocks to try a mean-reversion model on.
---

# Scan for candidates

## 1. Choose the scan, and run first

- Single stocks that return to their own average: `start_mr_scan`.
- Pairs of stocks that move together: `start_pairs_scan`.

Ask nothing before the first run. Run with sensible defaults and name them in one line with the result: the S&P 500 when the user names no index (the widest; the NASDAQ 100 and the Dow 30 are narrower and quicker), three years of daily history unless the user says otherwise, and the filters' own defaults. The user's own changes come after the result, as next steps. Pass the user's own words as `question`: the report is titled by it.

Each filter is `{"type": ..., "params": {...}}`, with the scanner's own setting names. For single stocks: `hurst` (`max_h`, default 0.45; `window`), `halflife` (`min_hl`, `max_hl` in days, default 5 to 100), `adf` (`max_pvalue`, default 0.05), `kpss` (`min_pvalue`, default 0.05), `variance_ratio`, `autocorr`, `volatility` and `liquidity` (`min_dv`, average daily dollar volume in millions). For pairs: `correlation` (`min_corr`), `cointegration` (`max_pvalue`), `spread_halflife`, `beta_stability`, `spread_hurst`, `johansen`. Leave a setting out to keep its default; a name the scanner does not know is refused with the filter's settings.

## 2. Run it and read it

The start waits a little for the scan: a scan that finishes inside it answers with `result` and `report_url` at once. A longer one answers that it is still going; then call the matching progress tool with the task id. Each call waits for the scan on QuanterLab's side, so call it again only when it answers that the scan is still going. The result's `candidates` block holds:

- `filters`: each filter's rule as the scanner applied it, and how many names passed it out of how many that reached it;
- `names`: up to ten, the ones that passed every filter first, then the closest misses, ranked, each with its own statistics rounded and, for a miss, the filter that stopped it.

Where the host shows cards, the scan shows in its own card, which fills in by itself: do not call `show_report_card` for it, and do not repeat what it shows. While it runs, say in one sentence what is running; never ask the user to come back, to wait or to check later. When it has finished, name the stocks that passed. When none or few passed, name the closest misses as misses, say which filter stopped most of them, and offer to loosen that filter. Quote the statistics as they come, never add precision, and never name a stock that is not in the list. Where no card shows, give `report_url`: the funnel and the names that passed are in the report, which opens without a login.

When the user asks to loosen the filters and scan again, compare the new `filters` rules with the old ones before saying the rerun was looser.

## 3. Test one

A scan finds candidates; only a test shows whether trading one would have worked. Where the card shows, it offers its next steps as choices the user can tap: ask which one they want, without listing them again. Where no card shows, offer two or three next steps to choose from, such as testing the first name that passed, and ask which one they want. Test the candidate the user picks with `start_stochastic_backtest`, for example an Ornstein-Uhlenbeck mean-reversion model (`list_stochastic_methods` lists the models and their settings).

A grid of settings, a sweep or "the best setting" of that model is one `start_stochastic_backtest` call with `sweep`, never many single runs: it is one task and one run of the user's allowance, and its answer carries the grid and the best setting's backtest.

Show that test the way the backtest-a-rule skill does: its own card, one sentence against holding the same stock, one caution, and the next steps to choose from.
