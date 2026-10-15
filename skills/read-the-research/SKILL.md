---
name: read-the-research
description: Answer questions from QuanterLab's published research studies and link to them. Use when the user asks what QuanterLab found, asks about an investment rule or market effect QuanterLab has studied (for example the overnight effect, dividend capture, the Piotroski F-score, buying the dips, the Fear and Greed Index, the turn of the month), or wants evidence on whether a well-known rule works.
---

# Read the research

QuanterLab publishes studies that test well-known investment rules. Each one is registered before it runs and walked window by window on point-in-time data, and each has its own page.

1. Call `search` with the topic in the user's own words. Pick the paper that matches; if several match, say which one you used.
2. Call `fetch` on it and answer from its text: what was tested, on which stocks and over which years, what came out, and the main limitation the paper states. Quote the paper's own headline rather than paraphrasing it into something stronger.
3. Always give the paper's link.
4. Keep the answer inside what the paper tested. A finding on large US stocks is not a finding on small caps or on other markets.
5. If the user wants to see it for themselves, offer two or three next steps to choose from: run the paper's own circuit with `start_test` and its `study` (the paper's id), which draws the circuit in the chat and runs it from the paper's last window to today; test a similar rule with the backtest-a-rule skill; or read the paper's page, where "Open in the lab" replays the study's own circuit when the page offers it.

If no paper covers the topic, say so. Never answer as if one did.
