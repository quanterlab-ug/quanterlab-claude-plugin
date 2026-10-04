# QuanterLab for Claude

QuanterLab is a quant research platform for testing trading strategies on historical data. This plugin connects Claude to your QuanterLab account and teaches it a careful research routine: backtest a rule and compare it with simply holding the stock, check the result with a walk-forward, scan an index for candidates, run the strategies you saved on the QuanterLab canvas, and read QuanterLab's published studies with their links.

## What you can ask

- "Backtest RSI(2) below 10 to buy and above 70 to sell on SPY over ten years. Did it beat just holding SPY?"
- "Is that result real or luck? Walk-forward it."
- "Scan the NASDAQ 100 for mean-reverting stocks."
- "Run my newest saved canvas strategy."
- "What did QuanterLab find about the overnight effect?"

## What's inside

- **The QuanterLab connector** at `https://quanterlab.com/mcp`. The first time, you sign in with your QuanterLab account.
- **Five skills** that tell Claude how to use it well: `backtest-a-rule`, `walk-forward-check`, `scan-for-candidates`, `run-a-saved-strategy` and `read-the-research`.

## Data

The plugin runs nothing on your computer and stores nothing. Everything goes through the connector to your QuanterLab account at quanterlab.com: the rules you ask to test, the tickers and their settings, and your searches of the published studies. Runs happen on QuanterLab's servers on past daily data. Results come back as summaries in words, with a few rounded figures for runs of a year or longer; a scan names the stocks or pairs that passed and the closest misses. The full reports stay on quanterlab.com.

QuanterLab places no orders, connects to no broker and moves no money. Nothing it returns is investment advice.

- Privacy policy: https://quanterlab.com/privacy-policy
- Terms: https://quanterlab.com/terms-and-conditions
- Support: contact@quanterlab.com

## License

MIT. See `LICENSE`.
