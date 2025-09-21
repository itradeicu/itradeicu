## 📘 Master Smart Dynamic Stoploss in Freqtrade: Only Cut Loss When Necessary
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In automated trading, stoploss is key to controlling losses and protecting capital.
Compared to fixed stoploss, Freqtrade's `custom_stoploss` allows **dynamic, flexible stoploss design**, helping you protect profits during uptrends and exit quickly during losses.

---

## ✳️ What is `custom_stoploss`?

`custom_stoploss` dynamically calculates and returns a stoploss price based on position status, time, market conditions, etc.

* **Return type:** `float` (new stoploss price)
* Return `-1` to continue using the default stoploss mechanism

> ⚠️ Note: Only for **stoploss**, not take profit. Use `custom_exit` for exit strategies.

---

### 📌 Typical Use Cases

* Trailing stoploss
* Multi-level dynamic stoploss (move stoploss up with profits)
* Profit protection at highs
* Tiered stoploss (wide to tight)

---

## 🧪 Example 1: Classic Trailing Stoploss

Move stoploss up with price, always keeping 2% below current rate:

```python
def custom_stoploss(self, trade, current_time, current_rate, current_profit, **kwargs) -> float:
    entry_price = trade.open_rate

    if current_profit < 0.05:
        # Before profit zone, initial stoploss at -2%
        return entry_price * 0.98

    # Once profit >5%, move stoploss to 2% below current rate
    return current_rate * 0.98
```

> ✅ Stoploss always rises with price; never decreases.
> ⚠️ Stoploss only moves up, never down.

---

## 🧪 Example 2: Profit-Tiered Stoploss

Define stoploss according to profit tiers:

* Profit 0\~5% → Stoploss -2%
* Profit 5\~10% → Stoploss 0% (break-even)
* Profit >10% → Lock in 5% profit

```python
def custom_stoploss(self, trade, current_time, current_rate, current_profit, **kwargs) -> float:
    entry_price = trade.open_rate

    if current_profit < 0.05:
        return entry_price * 0.98  # Stoploss -2%
    elif current_profit < 0.10:
        return entry_price  # Break-even
    else:
        return entry_price * 1.05  # Lock 5% profit
```

---

## 🧪 Example 3: Tolerate Small Drawdowns

Some coins are prone to quick pullbacks (“stop hunting”). Set stoploss only if loss exceeds 3%:

```python
def custom_stoploss(self, trade, current_time, current_rate, current_profit, **kwargs) -> float:
    if current_profit < -0.03:
        return current_rate  # Stoploss at current price
    return -1  # Use default stoploss
```

---

## 📊 Stoploss vs Take Profit

| Feature       | Take Profit (`custom_exit`)       | Stoploss (`custom_stoploss`)  |
| ------------- | --------------------------------- | ----------------------------- |
| Trigger exit  | ✅ Immediate sell                  | ✅ Set new stoploss price      |
| Frequency     | Evaluated each period             | Evaluated each period         |
| Purpose       | Lock profits                      | Limit loss / protect capital  |
| Return value  | True / False / float              | Stoploss price (float) / -1   |
| Complementary | Can work with `custom_exit_price` | Can work with `trailing_stop` |

---

## 🧰 Suggested Use with `trailing_stop`

```json
"trailing_stop": true,
"trailing_stop_positive": 0.02,
"trailing_stop_positive_offset": 0.04
```

* Let `custom_stoploss` manage **initial stoploss**,
* Then let `trailing_stop` take over to protect profits as price rises.

---

## 🧭 Configuration Tips

* Define `custom_stoploss` in your strategy; no extra activation needed.
* Recommended settings:

```json
"stoploss": -0.99,  // Avoid default stoploss triggering
"trailing_stop": false // Or coordinate logic if using trailing stop
```

---

### 📈 Trailing Stop Illustration

| Stage         | Price  | Stoploss | Notes                  |
| ------------- | ------ | -------- | ---------------------- |
| Initial entry | 100    | 98       | Initial stoploss       |
| Price ↑       | 105    | 102.9    | Stoploss moves up      |
| Price ↑       | 110.25 | 107.945  | Locking more profit    |
| Price ↑       | 115.76 | 113.45   | Continue trailing stop |

---

## 📌 Key Reminders

* Return value is **stoploss price**, not percentage.
* Return `-1` to skip changes; default stoploss applies.

---

## 📦 Summary

* `custom_stoploss` is one of Freqtrade’s most powerful risk management tools
* Supports trailing stop, multi-level logic, and profit protection
* Combined with `trailing_stop` and `custom_exit`, it forms a **complete capital defense system**
