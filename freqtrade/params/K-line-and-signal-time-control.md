# Don’t Get Fooled by Old Signals! Mastering Signal Timing in Freqtrade
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In strategy development, the timeliness of signals has a huge impact on results. Freqtrade provides several parameters to help you precisely control signal timing and data processing frequency, preventing premature or delayed signals from causing missed opportunities.

---

## ⏳ `ignore_buying_expired_candle_after` — Ignore Expired Buy Signals

```python
ignore_buying_expired_candle_after = 30  # unit: seconds
```

* Controls how long (in seconds) a buy signal remains valid after a candle has closed.
* For example, if set to 30, signals from a closed candle are still accepted within 30 seconds.
* Beyond this time, buy signals based on the closed candle are ignored, preventing outdated signals from triggering wrong entries.

**Use cases:**

* Prevent frequent trades caused by outdated signals in highly volatile markets.
* Fine-tune response time for high-frequency strategies to avoid missing optimal entries.

---

## ⏰ `process_only_new_candles` — Execute Logic Only on New Candles

`process_only_new_candles` is a boolean parameter in Freqtrade strategies that controls whether functions like `populate_indicators` run only when a new candle is completed. Default is `True`.

#### Example

```python
class MyStrategy(IStrategy):
    process_only_new_candles = True  # Run only after new candle closes

    timeframe = '5m'
    stoploss = -0.1
    minimal_roi = {"0": 0.05}

    def populate_indicators(self, dataframe, metadata):
        # Indicators calculated only once per 5m candle
        dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)
        return dataframe
```

* Controls whether indicators and signals are computed only after a candle is fully closed.
* Default `True`: avoids noisy signals from incomplete candles.
* If `False`: indicators are recalculated during candle formation, useful for high-frequency data needs but may increase false signals and CPU load.

### Behavior Comparison

| Value | Behavior                                                 |
| ----- | -------------------------------------------------------- |
| True  | Executes only after each candle closes                   |
| False | Continuously recalculates, signals may change frequently |

---

## ✅ Summary Table

| Parameter                            | Description                                     | Recommended Default         |
| ------------------------------------ | ----------------------------------------------- | --------------------------- |
| `ignore_buying_expired_candle_after` | Set expiration for old candle buy signals       | `0` (disabled) or as needed |
| `process_only_new_candles`           | Run logic only on closed candles to avoid noise | `True` (recommended)        |
