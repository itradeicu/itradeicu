# 🎯 How to Precisely Control Buy & Sell Prices? Practical `entry/exit_pricing` Configuration
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In trading, **the placement price of your orders determines execution efficiency and slippage risk**.
Freqtrade provides the `entry_pricing` and `exit_pricing` settings, allowing you to finely control limit order pricing logic.

This guide explains how to use order book data to implement precise order placement strategies, including depth filtering, to improve order quality and strategy robustness.

---

## 💡 Why configure `entry_pricing` / `exit_pricing`?

By default, limit orders are often placed at the current price or the candle close. This approach may:

* Fail to execute or deviate from the ideal price during **volatile markets**
* **Struggle with low liquidity coins**, causing stuck orders
* Increase slippage, negatively affecting strategy performance

By configuring `entry_pricing` and `exit_pricing`, you can:

✅ Place orders at bid/ask prices according to real order book
✅ Control the order book level (1st level, 2nd level, etc.)
✅ Enable depth filtering to **avoid placing orders during abnormal market movements**

---

## 📥 `entry_pricing` — Buy Order Pricing Configuration

```json
"entry_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 1,
  "price_last_balance": 0.0,
  "check_depth_of_market": {
    "enabled": false,
    "bids_to_ask_delta": 1
  }
}
```

#### 📋 `entry_pricing` / `exit_pricing` Parameter Table (with defaults)

| Parameter                       | Description                                                                                                                                                    | Default  |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `price_side`                    | Order direction: <br>• `bid`: buy side (bid price) <br>• `ask`: sell side (ask price) <br>• `same`: buy → bid, sell → ask <br>• `other`: buy → ask, sell → bid | `"same"` |
| `use_order_book`                | Use order book pricing: <br>• `true`: use live order book <br>• `false`: use candle close (not recommended)                                                    | `true`   |
| `order_book_top`                | Choose order book level: <br>• `1`: level 1 (best price) <br>• `2`: level 2, etc.                                                                              | `1`      |
| `price_last_balance`            | Offset from base price: <br>• Negative → more conservative <br>• Positive → more aggressive                                                                    | `0.0`    |
| `check_depth_of_market.enabled` | Enable depth filtering to prevent orders when bid/ask spreads are abnormal                                                                                     | `false`  |
| `bids_to_ask_delta`             | Maximum allowed bid/ask spread; orders are ignored if spread exceeds this (currency price unit)                                                                | `0.001`  |

---

## 📤 `exit_pricing` — Sell Order Pricing Configuration

```json
"exit_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 1
}
```

#### 📋 `exit_pricing` Parameter Table

| Parameter        | Description                                                                    | Recommended Default |
| ---------------- | ------------------------------------------------------------------------------ | ------------------- |
| `price_side`     | Sell order direction, same options as `entry_pricing`                          | `"same"`            |
| `use_order_book` | Use live order book pricing (`true`) or candle price (`false`)                 | `true`              |
| `order_book_top` | Order book level; smaller → faster execution, larger → better price but slower | `1`                 |

---

## 📊 Order Placement Behavior Comparison

| Scenario                   | Order Side                              | Order Book Level | Execution Speed | Slippage Control | Risk                 |
| -------------------------- | --------------------------------------- | ---------------- | --------------- | ---------------- | -------------------- |
| `same + order_book_top=1`  | Own side (buy → bid1, sell → ask1)      | Level 1          | Medium          | Low              | Medium               |
| `other + order_book_top=1` | Opposite side (buy → ask1, sell → bid1) | Level 1          | Fast            | Medium           | High (more slippage) |
| `same + order_book_top=2`  | Own side, level 2 (bid2/ask2)           | Level 2          | Slow            | Lower            | Low                  |
| `use_order_book=false`     | Candle price, no order book             | —                | Unstable        | Cannot control   | High                 |

---

## ✅ Recommended Configuration (Stable / Most Strategies)

```json
"entry_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 1,
  "check_depth_of_market": {
    "enabled": true,
    "bids_to_ask_delta": 1
  }
},
"exit_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 1
}
```

> Use together with `order_types.entry = "limit"` and `order_types.exit = "limit"`.

---

## 🧪 Practical Recommendations

| Strategy Type             | Recommended Setup                                                   |
| ------------------------- | ------------------------------------------------------------------- |
| Conservative orders       | `same + top=1`, enable depth filter to control execution & slippage |
| High-frequency arbitrage  | `opposite + top=1`, maximize fill rate, tolerate moderate slippage  |
| Low liquidity coins       | `top=1~2` + depth check, avoid abnormal prices                      |
| Aggressive low-price buys | `same + top=2~3`, combine with `price_last_balance`                 |

---

## 🧪 Example: Low-Price Buy + Conservative Sell

Trading **PEPE/USDT** on Binance (volatile, medium liquidity):

* **Buy:** aim for slightly lower price without sacrificing execution
* **Sell:** safely place at ask1 for fast exit

```json
"order_types": {
  "entry": "limit",
  "exit": "limit"
},

"entry_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 2,
  "price_last_balance": -0.000001,
  "check_depth_of_market": {
    "enabled": true,
    "bids_to_ask_delta": 0.0005
  }
},

"exit_pricing": {
  "price_side": "same",
  "use_order_book": true,
  "order_book_top": 1
}
```

---

## 🔍 Simulated Order Book Snapshot

| Bids (Buy)        | Asks (Sell)       |
| ----------------- | ----------------- |
| 0.00011300 (bid1) | 0.00011320 (ask1) |
| 0.00011280 (bid2) | 0.00011330 (ask2) |
| 0.00011270 (bid3) | 0.00011340 (ask3) |

### 📥 Entry Logic

* `order_book_top = 2` → bid2: `0.00011280`
* `price_last_balance = -0.000001` → final price: `0.00011279`
* Check if bid/ask spread (`0.00011320 - 0.00011300 = 0.00020`) < `bids_to_ask_delta` → allow order

✅ Final buy order: `0.00011279`

### 📤 Exit Logic

* `order_book_top = 1` + `price_side = same` → ask1: `0.00011320`

✅ Final sell order: `0.00011320` → fast execution, low slippage

---

### ✅ Usage Recommendations

| Goal                  | Entry Pricing                             | Exit Pricing   |
| --------------------- | ----------------------------------------- | -------------- |
| Buy cheaper           | `same + top=2~3 + price_last_balance < 0` | -              |
| Fast execution        | `same + top=1`                            | `same + top=1` |
| Avoid abnormal orders | `check_depth_of_market.enabled = true`    | -              |

---

## 📌 Summary

| Config                  | Controls                                       | Suggested Default                         |
| ----------------------- | ---------------------------------------------- | ----------------------------------------- |
| `entry_pricing`         | Buy order placement                            | Use order book same/level1 + depth filter |
| `exit_pricing`          | Sell order placement                           | Use order book same/level1                |
| `order_book_top`        | Which order book level                         | 1 (conservative), 2+ (lower price)        |
| `bids_to_ask_delta`     | Max order book spread filter                   | 1 (unit depends on market)                |
| `check_depth_of_market` | Prevent abnormal orders during volatile swings | Enabled                                   |

---

## ⚠️ Notes

* These settings only apply to **limit orders** (`order_types.entry = "limit"`).
* Market orders completely ignore these parameters.
* Frequent order book queries may hit exchange rate limits; adjust `enableRateLimit` accordingly.
* Some exchanges (e.g., HTX) may have delayed order book data; consider execution lag.

---

Good order placement is part of the strategy. Proper use of `entry_pricing` and `exit_pricing` **reduces slippage and improves execution efficiency**, making your strategies smarter and more reliable.

