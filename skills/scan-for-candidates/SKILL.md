---
name: scan-for-candidates
description: Find stocks that tend to snap back to their average, or pairs of stocks that move together, by scanning an index on QuanterLab, then test one of them. Use when the user asks for mean-reverting stocks, pairs trading candidates, cointegrated pairs, or stocks to try a mean-reversion model on.
---

# Scan for candidates

## 1. Choose the scan

- Single stocks that return to their own average: `start_mr_scan`.
- Pairs of stocks that move together: `start_pairs_scan`.

If the user did not name an index, ask: the S&P 500 is the widest, the NASDAQ 100 and the Dow 30 are narrower. Use three years of daily history unless the user says otherwise. `describe_capability` and `list_stochastic_methods` help when the request needs a particular filter or model.

## 2. Run it and read it

Poll the matching progress tool until it finishes; its last answer carries the result (or call `summarize_workflow` with the task id). QuanterLab says how many names passed; the names themselves are in the full report on QuanterLab, behind the `dashboard_url`. Never make up names that passed.

If very few or none passed, say the filters were probably too strict for that index and offer a looser scan, rather than presenting an empty result as a finding.

## 3. Test one

A scan finds candidates; only a test shows whether trading one would have worked. Offer to test a candidate the user picks from the report with `start_stochastic_backtest`, for example an Ornstein-Uhlenbeck mean-reversion model (`list_stochastic_methods` lists the models and their settings). Report that test the way the backtest-a-rule skill does: the answer first, one caution, the link.
