# 📘 Chapter 4: "How to Reliably Test Strategies? Complete Guide to Freqtrade Backtesting Command"

This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu) for more benefits.&#x20;

Backtesting is one of the **most critical steps** in strategy development. By simulating your strategy on historical market data, you can effectively evaluate its performance and determine whether it’s worth deploying in live trading.

This article provides a detailed guide to Freqtrade’s `backtesting` command, including usage, common parameters, data handling, result analysis, multi-processing acceleration, and Docker usage.

***

## 🔁 1. What is Backtesting and Why Is It Important?

Backtesting is the process of running your strategy on **historical data as “simulated trades”**, aiming to understand how the strategy would have performed in past markets.

A quality backtest can answer questions such as:

* Is the risk/reward ratio reasonable?
* Is the win rate stable enough?
* Is the strategy overfitted? (May fail in live trading)
* Which parameters most significantly affect profits?

***

## 🚀 2. Basic Backtesting Command Structure

```bash
freqtrade backtesting \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timeframe 15m \
  --timerange 20220101-20230701
```

### Parameter Details

| Parameter     | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| `--config`    | Path to configuration file (includes pairs, timeframe, etc.) |
| `--strategy`  | Strategy class name                                          |
| `--timeframe` | Timeframe, e.g., 15m, 1h                                     |
| `--timerange` | Backtesting period (format YYYYMMDD-YYYYMMDD)                |

Optional Parameters:

| Parameter      | Description                                         |
| -------------- | --------------------------------------------------- |
| `--export`     | Export detailed trade data as CSV                   |
| `--stats-file` | Output result statistics as JSON                    |
| `--processes`  | Run multiple processes in parallel to improve speed |

***

## 💡 3. Pre-Backtesting Preparation

Backtesting isn’t just running a command. Make sure to:

1. ✅ Download historical data for the target timeframe:

```bash
freqtrade download-data --timeframes 15m --timerange 20220101-20230701
```

2. ✅ Place strategy files in `user_data/strategies/` with correct class names.
3. ✅ Ensure `config.json` is correctly set, including:
   * Correct trading pairs
   * Correct exchange
   * `stake_currency` set to USDT, BTC, etc.

***

## 🧪 4. Backtesting Output Explanation

After backtesting, Freqtrade will log information including:

| Metric               | Meaning                               |
| -------------------- | ------------------------------------- |
| `Total profit`       | Strategy’s final net profit           |
| `Total trades`       | Number of trades executed             |
| `Win / loss ratio`   | Win rate (ratio of profitable trades) |
| `Sharpe Ratio`       | Risk-adjusted return                  |
| `Avg trade duration` | Average holding period                |
| `Drawdown`           | Maximum drawdown                      |
| `Profit factor`      | Profit factor (profit / loss)         |

***

## 🖼️ 5. Visual Backtesting (`backtesting-show`)

Freqtrade provides `backtesting-show` to visualize buy/sell points and strategy behavior:

```bash
freqtrade backtesting-show \
  --config user_data/config.json
```

📍 Displays the strategy’s equity curve, trade markers, positions, etc.

***

## 🧩 6. Common Backtesting Issues

| Issue                 | Possible Reason                                       |
| --------------------- | ----------------------------------------------------- |
| No trades executed    | Strategy signals too strict, or missing data          |
| Data not available    | Data not downloaded or timerange does not cover       |
| Indicators are NaN    | Wrong indicator settings / not suitable for timeframe |
| Suspected overfitting | High backtest profit but live loss; use forward test  |

***

## 🧠 7. Multi-Processing for Faster Backtesting (`--processes`)

For multiple pairs or complex strategies, `--processes` can speed up backtesting:

```bash
freqtrade backtesting \
  --config user_data/config.json \
  --strategy MyStrategy \
  --processes 4
```

🧪 Generally, set to half to all of CPU cores. For example, on an 8-core machine, set 4–8.

***

## 🐳 8. Backtesting in Docker

If running Freqtrade in Docker:

```bash
docker compose run --rm freqtrade backtesting \
  --config /quants/freqtrade/user_data/config.json \
  --strategy MyStrategy \
  --timeframe 15m \
  --timerange 20220101-20230701
```

Ensure `user_data/` is mounted to `/quants/freqtrade/user_data/` in the container.

***

## 📊 9. Exporting Backtesting Results

To save detailed trades:

```bash
--export user_data/backtest_result.csv
```

Or export strategy performance stats as JSON:

```bash
--stats-file user_data/backtest_stats.json
```

***

## ✅ 10. Recommended Analysis Metrics

Focus on these after backtesting:

* Total profit: Core metric
* Drawdown: Risk exposure
* Win rate / Risk-reward ratio: Stability
* Average trade profit: Value per trade
* Profit factor: Worthiness of trading

Combine with chart analysis:

```bash
freqtrade plot-dataframe --config user_data/config.json --strategy MyStrategy --timerange 20220101-20230101
```

***

## 📌 Summary

Freqtrade’s backtesting system is powerful and flexible, suitable for a complete strategy research and validation workflow.

| Step                     | Tool / Command                        |
| ------------------------ | ------------------------------------- |
| Download historical data | `download-data`                       |
| Write strategy class     | `new-strategy`                        |
| Run backtest             | `backtesting`                         |
| Visualize results        | `backtesting-show` / `plot-dataframe` |
| Accelerate performance   | `--processes` for multi-processing    |
| Containerized use        | Docker commands                       |
