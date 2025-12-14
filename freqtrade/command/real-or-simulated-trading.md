# 📘 Chapter 2: "Live or Simulated Trading? Freqtrade trade Command Explained"

This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu) for more benefits.&#x20;

`freqtrade trade` is the core command to start a live or simulated trading bot, and it’s the key step for deploying automated trading in practice.

This article provides an in-depth guide to the `trade` command, covering usage, common parameters, configuration tips, and Docker deployment, ideal for those preparing for live trading or just finishing strategy backtesting.

***

## 🚀 1. Basic Syntax

```bash
freqtrade trade \
  --config user_data/config.json \
  --strategy MyStrategy \
  --dry-run
```

### Parameter Explanation

| Parameter    | Description                                                         |
| ------------ | ------------------------------------------------------------------- |
| `--config`   | Path to the configuration file                                      |
| `--strategy` | Strategy class to use                                               |
| `--dry-run`  | Enable dry-run (simulated trading) mode, **recommended by default** |
| `--logfile`  | Path to save log output                                             |
| `--db-url`   | Specify database file or connection (for storing trade history)     |
| `--userdir`  | Set user directory path (default `user_data/`)                      |

***

## 🧪 2. What is Dry-run Mode?

Dry-run mode = no real orders, only simulated orders and log recording.

Suitable for:

* ✅ Strategy verification (ensure logic is correct)
* ✅ Test run before live deployment
* ✅ Avoid real losses from misconfiguration

When ready for live deployment, disable the parameter:

```bash
freqtrade trade --config user_data/config.json --strategy MyStrategy
```

> ⚠️ It is recommended to run dry-run for at least 7 days before going live!

***

### 🧩 3. Multi-strategy Support

You can load multiple strategies or strategy paths via command-line arguments:

```bash
freqtrade trade \
  --config user_data/config.json \
  --strategy-path user_data/strategies \
  --strategy MyStrategyA MyStrategyB
```

Or combine multiple config files (for multi-account / multi-platform):

```bash
freqtrade trade -c spot_config.json -c futures_config.json
```

Notes for multi-strategy:

* All strategies must be importable from the specified directory
* Use `--strategy-path` to specify additional paths if needed

***

## 🐳 4. Running `trade` in Docker

Running in a Docker container is safer and easier to deploy, recommended with docker-compose:

```bash
docker compose run --rm freqtrade trade \
  --config /quants/freqtrade/user_data/config.json \
  --strategy MyStrategy \
  --dry-run
```

Example `docker-compose.yml`:

```yaml
services:
  freqtrade:
    image: freqtradeorg/freqtrade:stable
    volumes:
      - ./user_data:/quants/freqtrade/user_data
    command: >
      trade
      --config /quants/freqtrade/user_data/config.json
      --strategy MyStrategy
      --logfile /quants/freqtrade/user_data/logs/freqtrade.log
```

After starting, you can access the Web UI to monitor trades:

```json
http://localhost:8888
```

***

## 🧷 5. Recommended Configuration Options

In `config.json`, it’s recommended to enable the following to ensure safety:

```json
{
  "dry_run_wallet": 1000,
  "dry_run": true,
  "max_open_trades": 3,
  "stake_currency": "USDT",
  "tradable_balance_ratio": 0.9,
  "timeframe": "15m"
}
```

Also recommended to configure:

* `exchange → key/secret`: real API keys needed for live trading
* `logging → logfile`: log output
* `db_url`: connect to SQLite/Postgres to store trade history

***

## ✅ 6. Pre-Live Checklist

| Check Item               | Recommendation                                   |
| ------------------------ | ------------------------------------------------ |
| Strategy backtested?     | ✅ At least 6 months of historical data           |
| Dry-run tested?          | ✅ Simulated run for 7+ days                      |
| Complete `config.json`?  | ✅ Include risk management, coins, leverage, etc. |
| API key set?             | ✅ And recommend IP whitelist                     |
| Monitor logs and errors? | ✅ Save logs using `--logfile`                    |

***

## 📌 Summary

The `trade` command is Freqtrade’s “final step” and the closest to real trading profits.

This article covered:

* Basic syntax and parameters of `trade`
* Benefits and recommended practices for Dry-run
* Multi-strategy / multi-config combinations
* Full Docker deployment workflow

Mastering these skills will help you transition from a strategy developer to a fully capable automated live trading operator.
