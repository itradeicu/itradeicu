# Sell When Up? Cut When Down? Unlocking Freqtrade’s Entry & Exit Logic
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In a `Freqtrade` strategy, entry and exit rules are the backbone of performance. Properly configuring take-profit, stoploss, and exit signals significantly improves both profitability and risk management. This guide focuses on the core parameters controlling entries, exits, and stop management.

---

## 🎯 `minimal_roi` — Time-Based Take-Profit

`minimal_roi` defines the minimum return thresholds based on holding time. Once the threshold is reached, the bot takes profit automatically.

```python
minimal_roi = {
    "30": 0.01,  # After 30 min, take profit at +1%
    "20": 0.02,  # After 20 min, take profit at +2%
    "0": 0.04    # Immediately take profit if +4% is hit
}
```

* Unit: minutes
* ROI maps time → required profit percentage
* Triggered automatically, with higher priority than custom exit signals

---

## 🛑 `stoploss` — Fixed Loss Threshold

Defines a hard stop for trades once losses reach a set percentage.

```python
stoploss = -0.10  # Stop out at -10%
```

* Protects capital by preventing uncontrolled losses
* Works with market orders for quick execution
* Acts as a safety net when combined with `trailing_stop`

---

## 🚪 `use_exit_signal` — Enable Custom Exit Signals

Controls whether `populate_exit_trend` is used for sell signals.

```python
use_exit_signal = True
```

* `True`: Strategy uses signals from `populate_exit_trend` (`exit_long` / `exit_short`)
* `False`: Ignores custom exit signals; exits only via ROI or stoploss
* Useful for strategies requiring fine-grained exit logic

---

## 💰 `exit_profit_only` — Exit Only in Profit

Prevents exit signals from triggering when a trade is losing.

* Applies **only** to `populate_exit_trend` signals
* If profitable → exit signals work as normal
* If losing → exit signals ignored (but stoploss / ROI still apply)
* Does **not** block:

  * `custom_exit()` logic
  * Hard stoploss
  * ROI-based exits

### Example

```python
use_exit_signal = True
exit_profit_only = True
```

| Current Profit | RSI > 70 (exit=1) | Sell? |
| -------------- | ----------------- | ----- |
| +5%            | ✅                 | ✅ Yes |
| +1%            | ✅                 | ✅ Yes |
| -3%            | ✅                 | ❌ No  |
| -10%           | ❌                 | ❌ No  |

---

## 🎚️ `exit_profit_offset` — Profit Offset Threshold

Adds a minimum profit requirement before exit signals trigger.

```python
exit_profit_offset = 0.01  # Require at least +1% profit
```

* Works with `exit_profit_only`
* Prevents premature exits at near-zero profit
* Helps maximize upside before selling

---

## 🧠 Example Strategy Snippet

```python
from freqtrade.strategy.interface import IStrategy
import talib.abstract as ta
import pandas as pd

class MyStrategy(IStrategy):
    timeframe = '15m'
    minimal_roi = {"0": 0.03}   # Always sell if profit ≥ 3%
    stoploss = -0.10            # Hard stop at -10%
    use_exit_signal = True
    exit_profit_only = True
    exit_profit_offset = 0.01   # Require at least +1% profit

    def populate_indicators(self, df: pd.DataFrame, metadata: dict) -> pd.DataFrame:
        df['rsi'] = ta.RSI(df, timeperiod=14)
        return df

    def populate_exit_trend(self, df: pd.DataFrame, metadata: dict) -> pd.DataFrame:
        df['exit'] = 0
        df.loc[df['rsi'] > 70, 'exit'] = 1  # Overbought → sell
        return df
```

---

## ⚙️ `use_custom_stoploss` — Dynamic Stoploss

Enables fully customizable stoploss logic via `custom_stoploss()`.

```python
use_custom_stoploss = True

def custom_stoploss(self, pair, trade, current_time, current_rate, current_profit, **kwargs) -> float:
    # Example: tighten stop once profit > 5%
    if current_profit > 0.05:
        return -0.01  # Allow only -1% drawdown
    return -0.05     # Default stop at -5%
```

⚠️ Notes:

* Only affects **stoploss behavior**, not take-profit
* Fixed `stoploss` is ignored once enabled
* Must return a **negative float** (e.g., `-0.05`)

---

## ✅ Quick Reference

| Parameter             | Function                     | Recommended Value                    |
| --------------------- | ---------------------------- | ------------------------------------ |
| `minimal_roi`         | Time-based take-profit rules | Strategy-dependent                   |
| `stoploss`            | Fixed loss threshold         | -0.10 (10%)                          |
| `use_exit_signal`     | Enable custom exit signals   | `True`                               |
| `exit_profit_only`    | Only sell if profitable      | `False` or `True` as needed          |
| `exit_profit_offset`  | Profit buffer before exiting | 0.01 (1%) typical                    |
| `use_custom_stoploss` | Enable dynamic stoploss      | `False` by default, enable if needed |

