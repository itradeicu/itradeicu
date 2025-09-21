# 📘 Smart Use of Leverage! Complete Freqtrade Leverage Strategy Guide
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In Freqtrade, when trading with leverage (especially on futures markets like Binance Futures), you can flexibly control leverage via configuration or strategy code.

This guide covers:

* `leverage` in the config file
* `leverage()` function in the strategy
* Practical usage and examples

---

## ⚙️ 1. Config Option `leverage` (Global Setting)

In `config.json`, you can set a global leverage for all trades:

```json
"leverage": 3
```

### ✅ Suitable Scenarios:

* Use the same leverage for all trades
* Futures mode trading (e.g., Binance Futures)

### ⚠️ Notes:

* Spot exchanges (e.g., Binance Spot) **cannot use leverage**
* Ensure the account has leverage permissions if using futures

---

## 🧠 2. `leverage()` Function (Dynamic Control in Strategy)

You can define a `leverage()` function in your strategy to dynamically specify leverage per trade. This is only active in Futures mode.

### 🔧 Function Definition:

```python
def leverage(
    self,
    pair: str,
    current_time: datetime,
    current_rate: float,
    proposed_leverage: float,
    max_leverage: float,
    entry_tag: Optional[str],
    side: str,
    **kwargs,
) -> float:
    ...
```

| Parameter           | Meaning                           |
| ------------------- | --------------------------------- |
| `pair`              | Trading pair name                 |
| `current_time`      | Current datetime                  |
| `current_rate`      | Current price                     |
| `proposed_leverage` | Default suggested leverage        |
| `max_leverage`      | Maximum allowed leverage for pair |
| `entry_tag`         | Optional signal tag               |
| `side`              | 'long' or 'short'                 |

---

## 🔍 Example 1: Fixed Leverage by Pair

```python
def leverage(self, pair: str, **kwargs) -> float:
    if "BTC" in pair:
        return 3.0
    elif "ETH" in pair:
        return 4.0
    else:
        return 2.0
```

---

## 📈 Example 2: Higher Leverage When Trend Strong

```python
def leverage(self, pair: str, current_time: datetime, current_rate: float,
            proposed_leverage: float, max_leverage: float, entry_tag: Optional[str],
            side: str, **kwargs) -> float:
    df = self.dp.get_analyzed_dataframe(pair, self.timeframe)[0]
    df['ma20'] = df['close'].rolling(20).mean()
    ratio = current_rate / df['ma20'].iloc[-1]

    if ratio > 1.02:
        return min(5.0, max_leverage)
    elif ratio < 0.98:
        return 1.0
    else:
        return proposed_leverage
```

---

## ✅ Recommended Practices

| Item                  | Recommendation                                              |
| --------------------- | ----------------------------------------------------------- |
| Global leverage       | Quick and simple, same leverage for all trades              |
| `leverage()` function | Flexible, adjust leverage per pair, signal, or trend        |
| Backtesting           | Leverage not enforced in backtests; assess risk accordingly |
| Combine with stoploss | Leverage increases risk; always set stoploss                |

---

## 🎯 Practical Example Strategy

```python
from freqtrade.strategy.interface import IStrategy
from pandas import DataFrame
from datetime import datetime
from typing import Optional

class LeverageStrategy(IStrategy):
    timeframe = '5m'
    leverage = 3  # default leverage for backtesting

    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['ma20'] = dataframe['close'].rolling(20).mean()
        return dataframe

    def populate_entry_trend(self, df: DataFrame, metadata: dict) -> DataFrame:
        df['enter_long'] = df['close'] > df['ma20']
        return df

    def populate_exit_trend(self, df: DataFrame, metadata: dict) -> DataFrame:
        df['exit_long'] = df['close'] < df['ma20']
        return df

    def leverage(self, pair: str, current_time: datetime, current_rate: float,
                 proposed_leverage: float, max_leverage: float, entry_tag: Optional[str],
                 side: str, **kwargs) -> float:
        df = self.dp.get_analyzed_dataframe(pair, self.timeframe)[0]
        ma = df['ma20'].iloc[-1]
        ratio = current_rate / ma

        if ratio > 1.02:
            return min(5.0, max_leverage)
        elif ratio < 0.98:
            return 1.0
        else:
            return 2.0
```

---

## 🧠 Summary

* Leverage can be defined in config or strategy for flexible control
* Dynamic function supports allocation per market, pair, signal, or trend
* **Always set a stoploss**; proper leverage usage is key to stable profits
