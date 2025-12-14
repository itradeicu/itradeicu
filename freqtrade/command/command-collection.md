# 📘 Chapter 1: "Freqtrade Common Commands Guide – Master Crypto Trading Bot Operations in One Article"
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
*“Master the key commands for cryptocurrency trading bots from beginner to live trading in one article!”*

Freqtrade is a powerful open-source cryptocurrency automated trading framework that supports strategy development, historical backtesting, parameter optimization, data analysis, and live trading.
For beginners, the **functions and uses of various commands may not be intuitive**. This article categorizes and explains all core Freqtrade commands, providing common use cases and CLI examples.

---

## 🎯 Command Structure Overview

Freqtrade’s CLI tool uses `freqtrade` as the main command, with different subcommands for different tasks:

```bash
freqtrade <subcommand> [options]
```

You can view the main command help via `freqtrade -h`, or see detailed parameters of a subcommand with `freqtrade <subcommand> -h`.

---

## 📦 Quick Command Classification

| Category               | Example Commands                          | Overview                                       |
| ---------------------- | ----------------------------------------- | ---------------------------------------------- |
| Data Handling          | `download-data`, `convert-data`           | Download / process historical market data      |
| Strategy Dev & Testing | `new-strategy`, `backtesting`, `hyperopt` | Create and test trading strategies             |
| Live Trading           | `trade`, `webserver`                      | Start the bot for trading or dry-run           |
| System Configuration   | `new-config`, `create-userdir`            | Initialize config and project structure        |
| Query & Diagnostics    | `show-trades`, `list-data`, `list-pairs`  | Query strategies, data, and trade history      |
| Visualization          | `plot-dataframe`                          | Chart-based visualization of strategy behavior |

---

## 🚀 1. Live and Dry-Run Trading Commands

#### `trade` – Start the trading bot (live or dry-run)

```bash
freqtrade trade \
  --config user_data/config.json \
  --strategy MyStrategy \
  --dry-run
```

* `--dry-run`: Simulated trading without real orders (recommended for beginners)
* `--db-url`: Specify database (stores trade history)
* `--logfile`: Path to save logs

> ⚠️ Ensure the strategy has been backtested and `config.json` is properly set before starting!

---

## 📥 2. Data Downloading and Processing Commands

#### `download-data` – Download historical market data

```bash
freqtrade download-data \
  --exchange binance \
  --pairs BTC/USDT \
  --timeframes 1h \
  --timerange 20230101-20230301
```

* `--exchange`: Supports multiple exchanges like binance, bybit, etc.
* `--pairs`: Can specify multiple trading pairs at once
* `--timeframes`: Supports 1m, 5m, 15m, 1h, 1d intervals

#### `convert-data` – Convert data format (CSV → JSON)

Use this when importing external data sources (e.g., CCXT, Kaggle) to Freqtrade format.

---

## 🧪 3. Strategy Development and Backtesting Commands

#### `new-strategy` – Create a strategy template

```bash
freqtrade new-strategy --strategy MyNewStrategy
```

Generates a `.py` file with annotated structure in `user_data/strategies/`.

#### `backtesting` – Backtest strategy performance

```bash
freqtrade backtesting \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timeframe 15m \
  --timerange 20220101-20230101
```

* Simulate historical trades to evaluate strategy PnL
* Can set timeframe, trading pairs, etc.
* Recommended to use with `backtesting-show` for chart visualization

#### `hyperopt` – Parameter optimization

```bash
freqtrade hyperopt \
  --config user_data/config.json \
  --strategy MyStrategy \
  --hyperopt-loss SharpeHyperOptLoss
```

* Automatically search for optimal parameter combinations (e.g., RSI thresholds, take-profit ratios)
* Supports multiple evaluation metrics (Sharpe, Sortino, pure profit, etc.)

---

## 📊 4. Data Visualization Command

#### `plot-dataframe` – Visualize strategy behavior in charts

```bash
freqtrade plot-dataframe \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timerange 20230101-20230201
```

* Generates HTML charts showing buy/sell points, candlesticks, and indicators
* Charts are saved in `user_data/plot/`

---

## ⚙️ 5. Configuration and Project Initialization Commands

#### `new-config` – Create a configuration file

```bash
freqtrade new-config --config user_data/config.json
```

Includes basic settings such as trading pairs, strategy name, risk management, and capital allocation.

#### `create-userdir` – Initialize project structure

```bash
freqtrade create-userdir --userdir user_data
```

Creates common directories (logs, data, strategies, configs).

---

## 🔍 6. Query and Utility Commands

| Command           | Description                                      |
| ----------------- | ------------------------------------------------ |
| `show-trades`     | Display trade / backtesting history              |
| `list-data`       | View available local historical data             |
| `list-pairs`      | Show trading pairs supported in current config   |
| `list-exchanges`  | List exchanges supported by Freqtrade            |
| `list-strategies` | Display strategy classes in `strategies/` folder |
| `list-timeframes` | Show supported timeframe formats                 |

---

## ✅ Commands to Master for Daily Use

```bash
freqtrade download-data
freqtrade backtesting
freqtrade hyperopt
freqtrade trade
freqtrade show-trades
```

---

## 🐳 Running Commands in Docker

Most commands also work under Docker:

```bash
docker compose run --rm freqtrade trade \
  --config /quants/freqtrade/user_data/config.json \
  --strategy MyStrategy
```

Ensure the mount paths in `docker-compose.yml` are correct.

---

## 📌 Summary

Freqtrade’s CLI covers the entire quantitative workflow: from data → backtesting → optimization → live trading, making it ideal for developers and strategy researchers.

Recommended workflow:

1. Download data (`download-data`)
2. Write strategy (`new-strategy`)
3. Backtest & tune (`backtesting + hyperopt`)
4. Run live (`trade + Web UI`)
5. Visualize results (`plot-dataframe`)

Master these commands, and you can independently manage a full cryptocurrency automated trading system!
