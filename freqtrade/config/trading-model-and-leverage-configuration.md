# ⚔️ Spot vs Futures: Trading Modes and Leverage Configuration
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
When using Freqtrade, the choice of trading mode directly affects your strategy logic, risk management, and order behavior. You can choose **spot trading** or **futures trading with leverage**. Different modes require different configuration parameters, such as `margin_mode`, `leverage`, and `liquidation_buffer`.

This guide explains the purpose, use cases, and practical considerations for these settings to help you build a sound risk management system.

---

## 💱 trading\_mode — Trading Mode

```json
"trading_mode": "spot"
```

* Determines whether Freqtrade operates on the spot or futures market.
* Options:

  * `"spot"`: Spot mode; buy and hold, cannot short.
  * `"futures"`: Futures mode; supports leverage, long and short positions, more flexibility but higher risk.

#### 🟢 Spot Mode Features

* Only supports buying low and selling high; suitable for bullish markets.
* Simple trading logic; no leverage management needed.
* Recommended for beginners or stable strategies.

#### 🔴 Futures Mode Features

* Supports long and short positions; suitable for sideways or bearish markets.
* Leverage amplifies gains and losses.
* Requires additional risk management: position sizing, liquidation buffers, isolated/cross margin, etc.

✅ Practical Tip:

* Beginners should start with `"spot"`.
* Once familiar, switch to `"futures"` and enable risk management parameters.

---

## 🧮 margin\_mode — Futures Margin Type

`margin_mode` only applies when `trading_mode: "futures"`. It has no effect in spot mode and will be ignored there. It sets the margin type per position.

```json
"trading_mode": "futures",
"margin_mode": "isolated"
```

Options:

| Value        | Meaning         | Notes                                                                                                               |
| ------------ | --------------- | ------------------------------------------------------------------------------------------------------------------- |
| `"isolated"` | Isolated Margin | Each position’s risk is independent; liquidation in one position does not affect others. Recommended for beginners. |
| `"cross"`    | Cross Margin    | All positions share account balance; mismanagement may liquidate the entire account.                                |

✅ Recommended:

* Use `"isolated"` for safer position management.

---

## 💥 leverage — Leverage Setting

Freqtrade supports dynamic leverage in strategies, and you can set different leverage for different pairs.

#### 📌 Example Strategy:

```python
def leverage(self, pair: str, current_time: datetime, current_rate: float,
             current_profit: float, current_cost: float, **kwargs) -> float:
    return 3.0  # 3x leverage
```

#### ⚠️ Notes

* Leverage is only applicable in `"futures"` mode.
* Must return a float.
* If `leverage()` is not implemented, Freqtrade will use the exchange account’s default settings.
* Some exchanges limit maximum leverage (e.g., Binance 20–125x).

✅ Practical Tip:

* Start with 2–5x leverage.
* Leverage amplifies both gains and losses, so combine with stop-loss and buffer mechanisms.

---

## 🛡️ liquidation\_buffer — Liquidation Buffer

```json
"liquidation_buffer": 0.05
```

* Only for `"futures"` mode.
* Reduces usable balance to avoid full exposure, lowering liquidation risk.
* 5% buffer means 5% of account balance is reserved.

#### 📌 Example:

| Total Account Balance | liquidation\_buffer | Max Usable Balance |
| --------------------- | ------------------- | ------------------ |
| 1000 USDT             | 0.05 (5%)           | 950 USDT           |
| 1000 USDT             | 0.2 (20%)           | 800 USDT           |

✅ Recommended:

* Beginners: 0.05–0.1
* High-leverage strategies: 0.2

---

## ✅ Recommended Configuration

```json
"trading_mode": "futures",
"margin_mode": "isolated",
"liquidation_buffer": 0.05
```

📌 Pair with strategy leverage:

```python
def leverage(self, pair, current_time, current_rate, current_profit, current_cost, **kwargs):
    return 3.0  # 3x leverage
```

---

## 📊 Mode Comparison

| Setting                      | Spot Mode | Futures Mode                       |
| ---------------------------- | --------- | ---------------------------------- |
| Shorting Supported           | ❌ No      | ✅ Yes                              |
| Leverage Supported           | ❌ No      | ✅ Yes                              |
| Risk Management Requirements | Low       | High (stop-loss, position control) |
| Recommended Margin Type      | -         | `"isolated"`                       |
| Leverage Logic Required      | ❌ No      | ✅ Recommended dynamic              |
| Supports liquidation\_buffer | ❌ No      | ✅ Strongly recommended             |

---

## 🔐 Live Trading Risk Management Recommendations

| Control                 | Suggested Practice                                               |
| ----------------------- | ---------------------------------------------------------------- |
| `margin_mode`           | `"isolated"` to prevent one liquidation affecting entire account |
| `leverage`              | Start with 2–3x; increase after backtesting                      |
| `liquidation_buffer`    | 0.05–0.2 to reduce full exposure risk                            |
| Stop-loss Configuration | Use `stoploss` or `custom_stoploss`                              |
| Position Limits         | Use `max_open_trades` to control open positions                  |

---

## 🧠 Summary

| Parameter            | Description                          | Recommended Range       |
| -------------------- | ------------------------------------ | ----------------------- |
| `trading_mode`       | Trading mode (spot/futures)          | `"spot"` or `"futures"` |
| `margin_mode`        | Margin type (futures only)           | `"isolated"`            |
| `liquidation_buffer` | Reserved account balance for futures | 0.05–0.2                |
| `leverage()`         | Dynamic leverage in strategy         | 2.0–5.0                 |
