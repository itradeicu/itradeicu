# 📘 Mastering Smart Order Pricing in Freqtrade: Full Guide to `adjust_entry_price` & `adjust_order_price`
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In Freqtrade, controlling order prices is crucial for minimizing slippage and improving strategy efficiency. The bot provides **two core hooks**:

* `adjust_entry_price` — Adjusts **initial buy orders** only (first-time entries).
* `adjust_order_price` — Adjusts **all orders** (buy, sell, add, reduce).

Using them properly allows you to implement **smarter limit orders, staggered entries, and better profit control**.

---

## 🎯 Function Overview

| Function             | Purpose                                  | When to Use        |
| -------------------- | ---------------------------------------- | ------------------ |
| `adjust_entry_price` | Modify price for first-time entry orders | ✅ First Buy Only   |
| `adjust_order_price` | Adjust price for all orders (entry/exit) | ✅ Every Order Call |

---

## 🧠 `adjust_entry_price` – First-Time Entry Only

This hook is called **once** when a strategy generates a first-time buy signal. Use it to tweak the entry price—e.g., avoid chasing a rising market.

### ✅ Example Use Cases

* Lower the first buy slightly to prevent buying at a peak.
* Fine-tune price relative to the order book.

### 🧪 Example Code

```python
def adjust_entry_price(self, pair: str, order_type: str, current_rate: float, proposed_rate: float, **kwargs) -> float:
    # Lower first entry price by 0.5%
    adjusted_price = proposed_rate * 0.995
    print(f"[adjust_entry_price] Original: {proposed_rate}, Adjusted: {adjusted_price}")
    return adjusted_price
```

### ⚠️ Notes

* Only triggered **for first-time entries**, not for add-ons.
* Must return the desired limit price.

---

## 🛠 `adjust_order_price` – Global Order Control

This hook affects **all orders**, including buy, sell, add-on, or reduce.

### ✅ Example Use Cases

* Apply consistent slippage adjustments across all orders:

  * Buy slightly lower
  * Sell slightly higher
* Dynamically adjust exit prices based on the current market.

### 🧪 Example Code

```python
def adjust_order_price(self, pair: str, is_buy: bool, current_price: float, proposed_price: float, **kwargs) -> float:
    if is_buy:
        # Buy: reduce price 0.3%
        return proposed_price * 0.997
    else:
        # Sell: increase price 0.3%
        return proposed_price * 1.003
```

---

## 🔍 Key Differences

| Aspect            | `adjust_entry_price`    | `adjust_order_price`                    |
| ----------------- | ----------------------- | --------------------------------------- |
| Trigger           | First-time buy only     | All orders (buy/sell/add/reduce)        |
| Control Direction | Buy only                | Buy & Sell                              |
| Control Scope     | First entry price       | All order prices                        |
| Recommended Usage | Fine-tune initial entry | Unified price adjustment for all orders |

---

## 📌 Edge Cases

| Scenario                       | `adjust_entry_price` called? | Recommended Approach                            |
| ------------------------------ | ---------------------------- | ----------------------------------------------- |
| First-time buy                 | ✅ Yes                        | Adjust initial limit price                      |
| Add-on buy (existing position) | ❌ No                         | Use `adjust_trade_position`                     |
| Sell (profit/stoploss)         | ❌ No                         | Use `custom_exit_price` or `adjust_order_price` |

---

## ✅ Best Practices

* Use `adjust_entry_price` **only for first entries**
* Use `adjust_order_price` for **all subsequent orders**
* Combine with `custom_exit_price` for precise exit pricing
* Never try to handle add-ons in `adjust_entry_price`—it won’t be called

---

## 📘 Simple Momentum Strategy Example

*Buy Signal:* Current close > previous close
*Sell Signal:* Current close < previous close

*Order Price Adjustments:*

* `adjust_entry_price`: -0.5% for first entry
* `adjust_order_price`: buy -0.3%, sell +0.3%
* `custom_exit_price`: sell +0.2% above current price

```python
from freqtrade.strategy.interface import IStrategy
from pandas import DataFrame

class ExampleStrategy(IStrategy):
    timeframe = '5m'

    order_types = {
        'entry': 'limit',
        'exit': 'limit',
        'stoploss': 'market',
    }

    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        return dataframe

    def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['enter_long'] = dataframe['close'] > dataframe['close'].shift(1)
        return dataframe

    def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['exit_long'] = dataframe['close'] < dataframe['close'].shift(1)
        return dataframe

    def adjust_entry_price(self, pair, order_type, current_rate, proposed_rate, **kwargs):
        return proposed_rate * 0.995  # first entry -0.5%

    def adjust_order_price(self, pair, is_buy, current_price, proposed_price, **kwargs):
        return proposed_price * (0.997 if is_buy else 1.003)  # buy -0.3%, sell +0.3%

    def custom_exit_price(self, pair, trade, current_time, current_rate, **kwargs):
        return current_rate * 1.002  # sell +0.2%
```

---

## 🧾 Summary

* `adjust_entry_price`: fine-tune **first entry price**
* `adjust_order_price`: control **all orders**, buy & sell
* `custom_exit_price`: precise exit pricing

By combining these three, you can:

* Avoid chasing high prices
* Protect against slippage
* Stagger orders intelligently
* Maximize profit on exits

> Mastering these hooks is a key step toward **professional-level limit order strategies** in Freqtrade.
