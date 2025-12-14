# 💰 Trading Currency and Fund Management Configuration
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In Freqtrade, fund configuration forms the foundation for all strategy operations. Whether trading spot or futures, parameters like `stake_currency`, `stake_amount`, and `tradable_balance_ratio` determine which currency is used per trade, how much capital is allocated, and how account risk is controlled.

Proper configuration helps strategies run more stably and safely, while poor settings may lead to order failures or liquidation during live trading.

---

## 🪙 stake\_currency — Trading Currency

```json
"stake_currency": "USDT"
```

* Specifies which currency is used as the base for each trade.
* Common options: `"USDT"`, `"BTC"`, `"ETH"`, etc.
* In spot trading, this determines the type of assets your account needs.
* In futures, it determines the margin currency.

✅ Practical Tips:

* Most strategies use `"USDT"` as `stake_currency` for general stability.
* If you only hold BTC and want to trade other coins directly with BTC, set it to `"BTC"`.

---

## 💵 stake\_amount — Amount per Trade

You can configure it as:

#### 1️⃣ Fixed Amount (Recommended for Beginners)

```json
"stake_amount": 100
```

* Uses up to 100 USDT per trade; the actual order may vary slightly due to price/position adjustments.
* Easier to control risk, providing more consistent backtest and live results.

#### 2️⃣ Dynamic Value `"unlimited"`

```json
"stake_amount": "unlimited"
```

* System uses available account balance (restricted by `tradable_balance_ratio`).
* More flexible for large accounts or complex strategy allocation.

⚠️ Notes:

* For futures accounts, ensure proper leverage is set; `stake_amount` alone does not control leverage.
* For multi-asset or multi-position strategies, careful position management is required to avoid using up all funds.

---

## 🧮 tradable\_balance\_ratio — Maximum Usable Balance

```json
"tradable_balance_ratio": 0.95
```

* Effective only when `stake_amount` is `"unlimited"`.
* Limits the strategy to use up to 95% of the account balance, keeping a 5% buffer.
* Prevents using up all funds and ensures subsequent signals can still place orders.

- 📌 Example:

  * Account balance: 1000 USDT, ratio = 0.95 → max usable: 950 USDT.

- ✅ Recommended Range:

  * 0.90 \~ 0.98;
  * More conservative values reduce liquidation risk.

---

## 🛡️ Live Trading Risk Control Recommendations

| Control Point      | Suggested Setting                                | Reason                                              |
| ------------------ | ------------------------------------------------ | --------------------------------------------------- |
| Initial Testing    | `"stake_amount": 50~100`                         | Fixed amount trading is more stable for observation |
| Multiple Positions | Use with `"max_open_trades"` limit               | Prevent overexposure, diversify risk                |
| Account Safety     | `tradable_balance_ratio < 1.0`                   | Keep a balance buffer to avoid liquidation          |
| Futures/Leverage   | Dynamic position control or `liquidation_buffer` | Prevent full-leverage liquidation                   |

---

## ✅ Example Configuration

```json
"stake_currency": "USDT",
"stake_amount": "unlimited",
"tradable_balance_ratio": 0.9
```

This setup means:

* Trades are executed in USDT;
* Each order uses a dynamic amount, but not more than 90% of total balance;
* Orders below 10 USDT are skipped;
* Suitable for intermediate or advanced users with risk management.

---

## ✅ Summary Table

| Parameter                | Description                          | Recommended / Tips                        |
| ------------------------ | ------------------------------------ | ----------------------------------------- |
| `stake_currency`         | Currency used for trading (buying)   | `"USDT"` most common                      |
| `stake_amount`           | Trade amount: fixed or `"unlimited"` | Fixed for beginners, dynamic for advanced |
| `tradable_balance_ratio` | Maximum account balance usage        | `0.9 ~ 0.98`                              |

> These settings form a basic capital safety line before deploying any strategy. Even the best strategy needs solid fund management.
