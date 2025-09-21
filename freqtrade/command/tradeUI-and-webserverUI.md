# 📘 Chapter 8: Understanding `tradeUI` vs `webserverUI` in Freqtrade
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
Although often used together, `trade` and `webserver` serve **completely different purposes** and have distinct startup logic. Here's a detailed comparison:

| Item                     | `freqtrade trade`                              | `freqtrade webserver`                                    |
| ------------------------ | ---------------------------------------------- | -------------------------------------------------------- |
| ✅ Startup Content        | Starts the trading bot (live or dry-run)       | Starts the visualization UI service (view data)          |
| ⚙️ Core Function         | Execute strategy, place orders, monitor market | Graphically display strategy operation/backtest results  |
| 🧠 Runs Strategy Logic   | ✅ Yes (real-time execution)                    | ❌ No (only reads data)                                   |
| 📦 Data Source           | Real-time market data & order execution        | Local SQLite database (or strategy output)               |
| ⏱ Use Case               | Real/simulated trading                         | Browser view of strategy performance/order info          |
| 🔗 Connects to Exchange  | ✅ Yes                                          | ❌ No                                                     |
| 📈 View Backtest Results | ❌ No                                           | ✅ Supports backtest visualization                        |
| 🚀 Browser Access        | Optional UI, mostly backend                    | [http://127.0.0.1:8080](http://127.0.0.1:8080) (default) |

---

## ✅ Use Case Distinction

### 1. `trade` is the bot engine

You can run **live trading** or **dry-run (simulation)**:

```bash
freqtrade trade \
  --config user_data/config.json \
  --strategy MyStrategy \
  --dry-run
```

* Continuously fetches real-time market data
* Executes strategy logic (buy/sell signals)
* Connects to the exchange and records orders, balances, profits
* Writes all data to a local database (SQLite)

> 💡 Without it, the bot **does nothing**!

---

### 2. `webserver` is the visualization interface

It only reads existing data and provides a browser-based UI:

```bash
freqtrade webserver \
  --config user_data/config.json \
  --username admin --password 123456
```

* Displays current positions, trade history, strategy names
* Can monitor running strategies
* Can also display backtest results (after `backtesting`)

> ❗ It **does not run strategies** or connect to exchanges!

![](https://fastly.jsdelivr.net/gh/bucketio/img15@main/2025/07/19/1752933518965-d816f2f1-f828-4207-bcb1-78e8732cfa9a.png)

---

## 🔄 Relationship Summary

* `trade` = the **execution engine**
* `webserver` = the **observer UI**

If you run only `webserver` without `trade`, you can **only see historical records**—no new trades will occur.

---

## 🧩 Practical Advice (Docker Setup)

```yaml
services:
  trader:
    image: freqtradeorg/freqtrade:stable
    command: >
      trade
      --config /quants/freqtrade/user_data/config.json
      --strategy MyStrategy

  ui:
    image: freqtradeorg/freqtrade:stable
    command: >
      webserver
      --config /quants/freqtrade/user_data/config.json
      --username admin
      --password 123456
    ports:
      - "8080:8080"
```

Then open your browser at:

```
http://localhost:8080
```

to access the trading bot dashboard.


