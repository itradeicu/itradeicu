# Freqtrade Strategy Not Running, Running Wrong, or Going Haywire? These Parameters Might Not Be Configured Properly
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
Before writing and running a Freqtrade strategy, there are several **basic parameters** you must understand.
These parameters control the strategy’s data timeframe, preloading behavior, concurrent trades, safety checks, etc., and directly affect execution and stability.

---

## ⏱️ `timeframe` — Main Timeframe

Sets the K-line interval used by the strategy. For example, `'5m'` means using 5-minute candles as the basis for signals and indicators.

```python
timeframe = '5m'  # Each candle represents 5 minutes
```

⚠️ Notes:

* Common values: `1m`, `5m`, `15m`, `1h`, `4h`, `1d`
* Determines the frequency of calculations and signal resolution
* Backtesting or live data must match this timeframe

---

## 🕐 `startup_candle_count` — Initial Loaded Candles

Specifies the minimum number of candles to preload when the strategy starts. Ensures indicators can be calculated fully and avoids distorted signals in the first few candles.

```python
startup_candle_count = 50  # Preload 50 candles at startup
```

⚠️ Notes:

* Usually set to the largest indicator period × 3\~5
* For example, RSI(14) typically requires at least 50 candles

---

## 📊 `max_open_trades` — Maximum Open Trades

Controls how many pairs the strategy can hold simultaneously. Helps prevent over-diversification, liquidation, or uncontrolled leverage.

```python
max_open_trades = 3  # Max 3 positions
```

⚠️ Notes:

* Set to 1 to test single-pair strategy performance
* Multi-pair strategies need careful capital allocation and risk management

---

## 🕛 `process_only_new_candles` — Execute Logic Only on New Candle

Controls whether the strategy logic triggers **only when a candle closes**. Default is `True`, which prevents repeated execution and improves stability.

```python
process_only_new_candles = True
```

| Value | Behavior                                                 |
| ----- | -------------------------------------------------------- |
| True  | Trigger only at candle close                             |
| False | Could trigger every second (high-frequency fluctuations) |

---

## 🧱 `disable_dataframe_checks` — Disable DataFrame Checks

Disables pandas DataFrame consistency checks to improve performance. Not recommended during early development.

```python
disable_dataframe_checks = False  # Enable checks (recommended)
```

⚠️ Notes:

* Disabling may hide hidden indicator errors
* Suitable for performance optimization stages

---

## 📉 `can_short` — Allow Shorting (Contracts Only)

Controls whether the strategy can open short positions. Only applicable to exchanges supporting **contract trading**, not spot.

```python
can_short = True
```

⚠️ Notes:

* If enabled, also configure `minimal_roi`, `stoploss`, `populate_exit_trend` for short logic
* Only valid in contract mode; using spot will cause errors

---

## ✅ Summary Table

| Parameter                  | Meaning                              | Recommended Default |
| -------------------------- | ------------------------------------ | ------------------- |
| `timeframe`                | Main strategy candle interval        | `'5m'`              |
| `startup_candle_count`     | Number of candles to load at startup | `50+`               |
| `max_open_trades`          | Maximum concurrent trades            | `3~5`               |
| `process_only_new_candles` | Trigger logic only at candle close   | `True`              |
| `disable_dataframe_checks` | Disable DataFrame checks             | `False`             |
| `can_short`                | Allow short positions (contracts)    | `False`             |
