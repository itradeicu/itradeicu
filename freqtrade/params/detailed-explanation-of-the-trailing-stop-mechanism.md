# Let Profits Run! Complete Freqtrade Trailing Stop Configuration Guide
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
Trailing Stop is a widely used risk management tool in trend trading. It automatically adjusts the stop-loss price as profits grow, locking in gains and limiting drawdowns. Freqtrade provides several parameters to finely control the behavior of trailing stops.

---

## ⚙️ `trailing_stop` — Enable Trailing Stop

```python
trailing_stop = True
```

When enabled, the system dynamically adjusts the stop-loss based on the highest profit point, allowing profits to run.

---

## 📈 `trailing_stop_positive` — Take-Profit Pullback Threshold

```python
trailing_stop_positive = 0.02  # 2%
```

A trailing stop is triggered when the price retraces more than this proportion from the peak profit.

---

## 🎯 `trailing_stop_positive_offset` — Activation Threshold

```python
trailing_stop_positive_offset = 0.05  # 5%
```

The trailing stop only activates after the profit reaches this minimum threshold.

---

## 🔄 `trailing_only_offset_is_reached` — Wait for Pullback to Trigger?

```python
trailing_only_offset_is_reached = True
```

* `True`: Stop-loss triggers **only after** the price retraces beyond `trailing_stop_positive`
* `False`: Trailing stop starts as soon as the profit reaches the offset

---

## 📊 Example: Buy Price at 100 USDT

```python
trailing_stop = True
trailing_stop_positive = 0.02
trailing_stop_positive_offset = 0.05
trailing_only_offset_is_reached = True
```

| Time | Current Price | Profit % | Highest Price | Offset Reached (5%) | Pullback Trigger (2%) | Stop Active | Estimated Stop Price |
| ---- | ------------- | -------- | ------------- | ------------------- | --------------------- | ----------- | -------------------- |
| t0   | 100           | 0%       | 100           | No                  | No                    | No          | —                    |
| t1   | 106           | +6%      | 106           | Yes                 | No                    | No          | —                    |
| t2   | 104           | +4%      | 106           | Yes                 | Yes                   | ✅ Yes       | 103.88               |
| t3   | 108           | +8%      | 108           | Yes                 | No                    | ✅ Yes       | 105.84               |
| t4   | 105.5         | +5.5%    | 108           | Yes                 | Yes                   | ✅ Yes       | 105.84               |
| t5   | 103           | +3%      | 108           | Yes                 | ✅ Price below stop    | ✅ Triggered | 🔔 Stop Activated    |

---

## 🚀 Practical Suggestions

| Strategy Type           | Recommended Parameters                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------- |
| Beginner                | `positive = 0.02`, `offset = 0.05`, `only_offset_is_reached = True` — safe and stable                   |
| High-volatility         | `positive = 0.01`, `offset = 0.03`, `only_offset_is_reached = False` — more responsive                  |
| High-frequency scalping | Consider disabling trailing stop and using fixed TP/SL, or use small pullback & offset for quick profit |

---

## ✅ Parameter Summary Table

| Parameter                         | Description                                             | Recommended Default |
| --------------------------------- | ------------------------------------------------------- | ------------------- |
| `trailing_stop`                   | Enable trailing stop mechanism                          | `True`              |
| `trailing_stop_positive`          | Max retracement from peak profit (take-profit pullback) | `0.02` (2%)         |
| `trailing_stop_positive_offset`   | Minimum profit to activate trailing stop                | `0.05` (5%)         |
| `trailing_only_offset_is_reached` | Wait for retracement to trigger trailing stop           | `True`              |

---

Using trailing stops wisely lets your **profits run while stopping risks in their tracks**. Proper parameter settings can significantly improve the strategy’s risk-reward ratio.
