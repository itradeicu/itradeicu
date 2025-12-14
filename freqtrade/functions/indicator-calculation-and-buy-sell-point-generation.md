# 📘 Profit Machine! Three Steps to Master Freqtrade Core Buy/Sell Signals – A Step-by-Step Guide to Writing Automated Trading Strategies
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In the Freqtrade strategy framework, `populate_indicators`, `populate_entry_trend`, and `populate_exit_trend` are the three most basic and commonly used functions. They are responsible for:

* ✍️ Calculating technical indicators (e.g., RSI, MACD, moving averages)
* 📈 Determining buy signals
* 📉 Determining sell signals

This article will explain the purpose, usage, code examples, and tips for these three functions, helping you build your own quantitative trading strategy from scratch.

---

## 1️⃣ `populate_indicators`: Core Function for Calculating Technical Indicators

### ✅ Purpose

This function calculates and fills technical indicators in the DataFrame, which are used for subsequent buy/sell signal decisions.

It takes historical OHLCV data as input and returns a DataFrame with additional indicator columns, such as RSI, MACD, SMA, EMA, etc.

### 🧠 Backtesting vs Live Trading

* **Backtesting**: `populate_indicators` is called once for the entire historical dataset.
* **Live Trading**: Called every time a new candle is generated (e.g., every minute or hour).

In live trading, you can add **real-time processing** here; in backtesting, you may iterate over rows with `for row in dataframe.iterrows()`.

### 💡 Example Code

```python
def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    # Calculate 14-period RSI
    dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)

    # Optional: row-wise processing for backtesting
    for index, row in dataframe.iterrows():
        if row['rsi'] < 30:
            # Logging or tagging
            pass

    return dataframe
```

---

## 2️⃣ `populate_entry_trend`: Buy Signal Logic

### ✅ Purpose

`populate_entry_trend` defines **when to enter a trade**. It generates a `buy` column in the DataFrame:

* `buy = 1` means this candle is a buy signal
* `buy = 0` (default) means no action

Freqtrade will execute a trade with a market or limit order when the condition is met.

### 🛠 Usage

You can use `.loc` to filter the DataFrame and set buy signals:

```python
dataframe.loc[condition_expression, 'buy'] = 1
```

### 💡 Example Code

```python
def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe['buy'] = 0  # Initialize as no buy

    # RSI below 30 triggers buy signal
    dataframe.loc[dataframe['rsi'] < 30, 'buy'] = 1

    return dataframe
```

### 📌 Tips

* Combine multiple conditions using `(cond1) & (cond2)`.
* Incorporate Bollinger Bands, moving averages, or other indicators to refine entries.

---

## 3️⃣ `populate_exit_trend`: Sell Signal Logic

### ✅ Purpose

`populate_exit_trend` defines **when to exit a trade**. It works similarly to `populate_entry_trend`, but sets the `sell` column:

* `sell = 1` triggers a sell
* `sell = 0` means no action

Freqtrade will sell when `sell=1`.

### 💡 Example Code

```python
def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe['sell'] = 0  # Initialize as no sell

    # RSI above 70 triggers sell signal
    dataframe.loc[dataframe['rsi'] > 70, 'sell'] = 1

    return dataframe
```

### 🧠 Tips

* Add volume or price pattern logic to improve exit accuracy.
* Combine with ROI or trailing stop to create a complete exit mechanism.

---

## 🧩 Example Strategy: Minimal RSI Strategy

A complete strategy class combining the three functions. Logic:

* Buy when RSI < 30 (oversold)
* Sell when RSI > 70 (overbought)

```python
from freqtrade.strategy import IStrategy
from pandas import DataFrame
import talib.abstract as ta

class RsiExampleStrategy(IStrategy):
    timeframe = '5m'
    stake_amount = 100
    minimal_roi = {"0": 0.1}
    stoploss = -0.2
    use_exit_signal = True

    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)
        return dataframe

    def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['buy'] = 0
        dataframe.loc[(dataframe['rsi'] < 30), 'buy'] = 1
        return dataframe

    def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['sell'] = 0
        dataframe.loc[(dataframe['rsi'] > 70), 'sell'] = 1
        return dataframe
```

---

## 🧾 Summary

| Function               | Purpose              | Returns                       |
| ---------------------- | -------------------- | ----------------------------- |
| `populate_indicators`  | Calculate indicators | DataFrame with new indicators |
| `populate_entry_trend` | Set buy signals      | DataFrame with `buy=1`        |
| `populate_exit_trend`  | Set sell signals     | DataFrame with `sell=1`       |

These three functions are the **core “trio”** of a strategy. You can further optimize and combine them to build your own trading system.

---

Next article will cover `custom_exit` vs `custom_exit_price` and how to use them together. Stay tuned!

