## 📘 Boost Your Profit by 10% with One Line? How to Use `custom_roi` for Precise Take Profit
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In Freqtrade strategies, `custom_roi` is a highly useful function that allows you to dynamically adjust your take profit target based on current position status, time, and profit.
Compared to static `minimal_roi`, `custom_roi` is more flexible and can help improve profitability and risk management.

---

## ⚙️ `custom_roi` Function Overview

`custom_roi` allows users to define custom take profit logic. It is called automatically every trading interval and returns a dynamic ROI target. Returning `0` triggers immediate exit, while returning `None` continues to use the default ROI.

#### Function Signature Example

```python
def custom_roi(self, trade, current_profit: float, current_time) -> float | None:
    """
    Custom take profit strategy
    Parameters:
    - trade: Current trade object with position info
    - current_profit: Current profit (float)
    - current_time: Current timestamp

    Returns:
    - float ROI target, or None to use default ROI
    """
    pass
```

---

## 🔍 Example: Dynamic Take Profit Based on Hold Time and Profit

```python
def custom_roi(self, trade, current_profit: float, current_time) -> float | None:
    # Calculate hold time in minutes
    hold_time = (current_time - trade.open_date_utc).total_seconds() / 60

    # If holding less than 30 minutes, use strict ROI: take profit at 5%
    if hold_time < 30:
        target_roi = 0.05
    else:
        # If holding more than 30 minutes, relax ROI to 10%
        target_roi = 0.10

    # Trigger take profit if current profit meets target
    if current_profit >= target_roi:
        print(f"[custom_roi] Take profit triggered, current profit {current_profit:.2%} >= target {target_roi:.2%}")
        return 0  # Exit immediately

    # Continue holding if target not reached
    return None
```

---

## ❓ Does `minimal_roi` Still Apply When Using `custom_roi`?

* Once `custom_roi` is implemented, `minimal_roi` is ignored; take profit logic is fully controlled by `custom_roi`.
* If `custom_roi` returns `None`, it falls back to the default `minimal_roi`.
* It is recommended to fully define take profit logic in `custom_roi` for consistent behavior.

---

## ✅ Practical Tips

* Use `custom_roi` to flexibly define take profit thresholds based on hold time and profit
* Add logging for easier debugging and strategy evaluation across market conditions
* Dynamic take profit is suitable for volatile or trending markets, enhancing profitability and risk control

---

## 🧠 Summary

* `custom_roi` is an essential extension interface for take profit management in Freqtrade
* Enables smart, dynamic take profit strategies
* Fully replaces static `minimal_roi`, increasing strategy flexibility and adaptability
* Well-designed take profit logic is key to risk management and maximizing returns
