# 📘 Freqtrade config.json Basics & Initialization
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
Before using Freqtrade for strategy backtesting, data downloading, or live trading, the most crucial step is to **create and configure the `config.json` file**. It serves as the “main command center” of the entire trading framework, determining which exchange to connect, how to trade, how strategies run, and how funds are allocated.

This guide explains the purpose, creation, structure, and editing tips for `config.json`, helping you get started quickly and avoid common pitfalls.

---

## 🧱 What is config.json?

`config.json` is the **main configuration file** for Freqtrade, centralizing all runtime parameters:

* Exchange account info (API Key / Secret / Password)
* Trading coins and amount settings
* Strategy rules (e.g., maximum open trades, long/short settings)
* Data timeframes and backtesting settings
* Limit / market order control
* Risk management settings (stop-loss, take-profit, slippage)
* Optional notification channels (Webhook, Telegram, etc.)

No matter if you are doing data analysis, backtesting, or live deployment, this file is indispensable.

---

## 🆕 How to generate config.json

Freqtrade provides a CLI tool to quickly generate a default template, ideal for beginners:

```bash
freqtrade new-config --config user_data/config.json
```

Notes:

* `--config` specifies the target path (official recommendation: `user_data/config.json`)
* If the directory does not exist, it will be automatically created
* After generation, you can edit it with any text editor

---

## 📂 Recommended project structure

Recommended folder layout:

```plaintext
freqtrade/
├── user_data/
│   ├── config.json         ← ✅ main config file
│   ├── strategies/         ← strategy scripts (.py)
│   ├── logs/               ← log outputs
│   └── ...
├── freqtrade/              ← Freqtrade core code / virtual environment
```

> Keeping `config.json` inside `user_data/` ensures consistent paths and easier backups.

---

## 🐳 Docker folder structure

When using Docker, the directory is controlled via **volume mapping**:

```plaintext
ft_userdata/
├── user_data/
│   ├── config.json         ← ✅ main config file
│   ├── strategies/         ← strategy scripts
│   ├── hyperopt/           ← hyperopt results
│   ├── logs/               ← log outputs
│   └── ...
├── docker-compose.yml      ← Docker service entry
```

> You can modify `user_data/config.json` to adjust runtime parameters without rebuilding the Docker image.

---

## 🔍 Beginner example

Basic `config.json` snippet:

```json
{
  "max_open_trades": 3,
  "stake_currency": "USDT",
  "stake_amount": 100,
  "tradable_balance_ratio": 0.95,
  "dry_run": true,
  "exchange": {
    "name": "binance",
    "key": "your_api_key",
    "secret": "your_api_secret",
    "password": "your_api_password"  // required by some exchanges, e.g., OKX, Kraken
  }
}
```

Field explanations:

* `max_open_trades`: Maximum simultaneous open positions
* `stake_currency`: Base trading currency (e.g., USDT)
* `stake_amount`: Amount used per trade
* `dry_run`: Enable simulated trading (true = no real funds used)

---

## 🛠️ Editing & debugging tips

1. **Check if the exchange API is valid**:

```bash
freqtrade list-markets --config user_data/config.json
```

| API Key Configured | list-markets works?   | Can trade? | Notes                                     |
| ------------------ | --------------------- | ---------- | ----------------------------------------- |
| ✅ Yes              | ✅ Yes                 | ✅ Yes      | Full functionality, live trading possible |
| ❌ No               | ✅ Depends on exchange | ❌ No       | Can only view market data                 |

2. **Initialize strategy check**:

```bash
freqtrade backtesting --config user_data/config.json
```

> This helps catch strategy or config errors before live trading.

---

## 🧠 Summary

| Key point        | Recommendation                                      |
| ---------------- | --------------------------------------------------- |
| File name        | Keep as `config.json`, recommended in `user_data/`  |
| Generate command | `freqtrade new-config --config path`                |
| Debugging        | Use `backtesting` or `list-markets` to check        |
| Version control  | ✅ Strongly recommend including `config.json` in Git |

---

This guide helps you **initialize config.json**, the foundation for running strategies, downloading data, and deploying to live trading.

