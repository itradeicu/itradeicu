# 📘 Sell Only When You Should! Freqtrade Quantitative Custom Exit Guide — Precise Exits, Profit Without Loss
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In automated trading, **exiting a trade is even more important than entering**.
`Freqtrade` provides two functions to help you build **smarter sell strategies**:

* `custom_exit`: Controls whether to exit at market price
* `custom_exit_price`: Controls the limit price for exit

---

## ✳️ Function 1: `custom_exit` — Control Whether to Sell (Market Order)

### Overview

`custom_exit()` determines whether to **exit immediately at market price**. Use it when you want to:

* Sell when reaching a target profit (take profit)
* Stop loss when exceeding a threshold
* Exit based on time or external signals

---

### 🧪 Example 1: 10% Take Profit, 5% Stop Loss

A common exit strategy:
**Sell if profit > 10%; stop loss if loss > 5%; otherwise, hold.**

```python
def custom_exit(self, trade, current_time, current_rate, current_profit, **kwargs) -> float | bool:
    """
    Logic:
    - Current profit > 10%, sell immediately (take profit)
    - Current profit < -5%, sell immediately (stop loss)
    - Otherwise, hold
    """
    if current_profit > 0.10:
        return True
    if current_profit < -0.05:
        return True
    return False
```

> ✅ Note: Returning `True` means “sell now,” `False` means “hold.”

---

## ✳️ Function 2: `custom_exit_price` — Set Limit Exit Price

### Overview

`custom_exit_price()` does not decide whether to exit; it sets the **desired limit price**. Useful for:

* Placing a slightly higher sell order to maximize profit
* Custom limit order exit strategies
* Getting a better price than market, though execution may be slower

---

### 🧪 Example 2: Limit Sell 1% Above Current Price

```python
def custom_exit_price(self, pair, trade, current_time, current_rate, current_profit, exit_tag, **kwargs) -> float | None:
    """
    If current profit > 5%, place a limit sell 1% above current price.
    Otherwise, return None (no limit order)
    """
    if current_profit > 0.05:
        return current_rate * 1.01  # 1% above current price
    return None
```

### ⚠️ Notes

Make sure `"exit": "limit"` is set in `order_types`:

```json
// Required for limit-based exit
"order_types": {
  "entry": "limit",
  "exit": "limit"
}
```

---

## 🔄 Can Both Functions Be Used Together?

Yes, and it works well:

* `custom_exit_price`: sets a limit order first
* If the price doesn’t fill in time, or market moves sharply…
* `custom_exit` triggers a **market exit** to lock in profit or stop loss

---

### 🧪 Example 3: Limit + Forced Exit for Extra Profit

```python
def custom_exit_price(self, pair, trade, current_time, current_rate, current_profit, exit_tag, **kwargs) -> float | None:
    # Place limit 1% above current price if profit > 5%
    if current_profit > 0.05:
        return current_rate * 1.01
    return None

def custom_exit(self, trade, current_time, current_rate, current_profit, **kwargs) -> float | bool:
    # Force market sell if profit > 15%
    if current_profit > 0.15:
        return True
    # Stop loss if loss > 6%
    if current_profit < -0.06:
        return True
    return False
```

---

## 🔍 Usage & Configuration Requirements

To make both functions work correctly, configure `config.json`:

```json
"order_types": {
  "entry": "limit",
  "exit": "limit"
},
"use_exit_signal": true
```

---

## 📊 Comparison Table: `custom_exit` vs `custom_exit_price`

| Feature         | `custom_exit`               | `custom_exit_price`                   |
| --------------- | --------------------------- | ------------------------------------- |
| Exit Type       | Market Order                | Limit Order                           |
| Immediate Exit  | ✅ Yes                       | ❌ No                                  |
| Execution Speed | Fast (may slip)             | Slower (better price)                 |
| Return Value    | True / False / float        | float or None                         |
| Prerequisite    | None                        | Must enable limit orders              |
| Recommended Use | Stop loss, fast take profit | Wait for higher price, slow arbitrage |

---

## 🎯 Summary Tips

* Want to sell fast? Use `custom_exit`
* Want to sell smart? Use `custom_exit_price`
* Combine both: place a limit order while keeping a safety exit line

---

📍Next Preview:
👉 Chapter 3: Smart Stop Loss — Using `custom_stoploss` + `trailing_stop` Together
Learn how to implement trailing stops, breakeven exits, and automated profit protection!

> If helpful, please like + save to support our updates 🙌
