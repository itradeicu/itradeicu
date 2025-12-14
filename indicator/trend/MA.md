# 📘 Comprehensive Analysis of SMA and EMA Indicators: Formulas, Calculations, Divergences, and Practical Strategies
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
## 1. Basic Introduction to SMA and EMA

**SMA (Simple Moving Average)**  
Refers to the arithmetic average of the closing prices of the past N K-lines, representing a smoothed trend of prices, suitable for trend judgment and support/resistance analysis.

**EMA (Exponential Moving Average)**  
Assigns higher weights to recent prices, with more sensitive responses, suitable for capturing short-term momentum and turning points.

---

### 1. What is SMA (Simple Moving Average)

**Definition:**  
SMA is the "Simple Moving Average," referring to the arithmetic average of the closing prices of the past N K-lines.  
It is commonly used for trend judgment to smooth price fluctuations.

**Formula:**

```
SMA = (C1 + C2 + ... + Cn) / n
```

Where:

* Cn: Represents the closing price of the nth K-line
* n: Period length, such as 5, 20, 60

---

### 2. What is EMA (Exponential Moving Average)

**Definition:**  
EMA is the "Exponential Moving Average," where more recent data has higher weight.  
It is more sensitive and responds faster than SMA.

**Formula (Recursive Formula):**

```
EMA_today = α × Price_today + (1 - α) × EMA_yesterday
```

Where:

* α (Smoothing Coefficient) = 2 / (n + 1)
* n is the period length
* EMA_yesterday is the EMA value from the previous period

---

### 3. Simulate Calculation with a Set of Data

We use the following 5 days' closing prices for simulation calculation:

```
Closing Prices (Close): [10, 12, 13, 14, 15]
Period n = 5
```

### 4. Calculate SMA:

```
SMA_5 = (10 + 12 + 13 + 14 + 15) / 5 = 64
64 / 5 = 12.8
```

---

### 5. Calculate EMA:

First, calculate the smoothing coefficient α:

```
α = 2 / (5 + 1) = 0.333...
```

Assume the first EMA value = first price (initialization):

```
EMA_1 = 10
```

Then recursively calculate:

```
EMA_2 = 0.333 × 12 + 0.667 × 10 = 10.67
EMA_3 = 0.333 × 13 + 0.667 × 10.67 ≈ 11.45
EMA_4 = 0.333 × 14 + 0.667 × 11.45 ≈ 12.30
EMA_5 = 0.333 × 15 + 0.667 × 12.30 ≈ 13.20
```

Final Results:

```
SMA_5 = 12.8
EMA_5 ≈ 13.20
```

---




## 2. Suitable Periods and Scenarios for EMA and SMA Respectively

| Type  | Recommended Periods           | Applicable Scenarios        | Characteristics         |
| --- | -------------- | ----------- | ---------- |
| SMA | Medium-to-Long Term (20/50/200) | Trend Judgment, Support/Resistance   | Smoother, Suitable for Long-Term Holding |
| EMA | Short-to-Medium Term (5/10/20)   | Short-Term Entry/Exit, Fast-Paced Markets | More Sensitive, Suitable for Quick In and Out |

---

## 3. Advantages and Disadvantages of SMA and EMA and Applicable Periods

| Characteristic     | SMA            | EMA          |
| ------ | -------------- | ------------ |
| Response Speed   | Slow              | Fast            |
| Smoothness    | High, Better Noise Filtering       | Low, More Sensitive Signals      |
| False Signal Probability  | Low              | High            |
| Practical Robustness  | High, Suitable for Stable Trend Trading     | Low, Susceptible to Slippage     |
| Recommended Periods   | Medium-to-Long Term (20/50/200) | Short-to-Medium Term (5/10/20) |
| Applicable Strategy Types | Trend Following, Medium-to-Long Holding      | Short-Term Momentum Capture, High-Frequency Trading  |

---

## 4. Golden Cross/Dead Cross Trading Signals

* **Golden Cross (Buy)**: Short-term moving average crosses above long-term moving average (e.g., EMA5 > EMA20)
* **Dead Cross (Sell)**: Short-term moving average crosses below long-term moving average (e.g., SMA10 < SMA60)
* **Trend Judgment**: Price above the moving average indicates an uptrend, otherwise a downtrend

### ✅ Trend Judgment (Price vs Moving Average)
| Condition      | Interpretation          |
| ------- | ----------- |
| Price > Moving Average | Uptrend / Support Zone |
| Price < Moving Average | Downtrend / Resistance Zone |

## 5. Divergence Signals
> Divergence is the phenomenon where the price trend is inconsistent with technical indicators (such as moving averages), usually indicating weakening momentum in the current trend, possibly signaling an impending trend reversal or adjustment.

Divergences mainly include the following types:

1. **Top Divergence (Bearish Divergence)**  
   Price makes a new high, but the indicator does not synchronously make a new high, implying weakening upward momentum, possibly leading to a downward reversal.

2. **Bottom Divergence (Bullish Divergence)**  
   Price makes a new low, but the indicator does not synchronously make a new low, indicating weakening downward momentum, possibly leading to a rebound or upward trend reversal.

3. **Hidden Divergence**  
   Price high or low does not make a new high/low, but the indicator shows the opposite trend, often used to judge trend continuation.

These types of divergences help traders identify potential trend turning points or continuation signals.




### **Price and Moving Average Divergence (Price-MA Divergence)**

**Formula:**

```text
div = (Price - MA) / MA × 100%
```

Represents the percentage deviation of the price from a certain moving average.  
If positive, the price is above the moving average; if negative, the price is below the moving average.

---

#### 🔢 Calculating SMA and EMA Divergences


Closing Prices (close) for the past 10 days:

```text
[100, 102, 101, 103, 105, 104, 106, 107, 108, 110]
```

Period n = 5, calculate EMA5:

Smoothing Coefficient:

$$
\alpha = \frac{2}{5+1} = 0.3333
$$

Assume Day 1 EMA5 initialized equal to Day 1 closing price, i.e., EMA_1 = 100.

---

### 1. Calculate EMA5 Sequence

Recursively calculate EMA5:

| Day | Closing Price (Price) | Calculation Process                                   | EMA5 Value |
| -- | ----------- | -------------------------------------- | ------ |
| 1  | 100         | Initialization                                    | 100.00 |
| 2  | 102         | 0.333×102 + 0.667×100 = 34+66.7        | 100.67 |
| 3  | 101         | 0.333×101 + 0.667×100.67 ≈ 33.67+67.12 | 100.79 |
| 4  | 103         | 0.333×103 + 0.667×100.79 ≈ 34.33+67.19 | 101.52 |
| 5  | 105         | 0.333×105 + 0.667×101.52 ≈ 35+67.68    | 102.68 |
| 6  | 104         | 0.333×104 + 0.667×102.68 ≈ 34.67+68.43 | 103.10 |
| 7  | 106         | 0.333×106 + 0.667×103.10 ≈ 35.33+68.75 | 104.08 |
| 8  | 107         | 0.333×107 + 0.667×104.08 ≈ 35.67+69.44 | 105.11 |
| 9  | 108         | 0.333×108 + 0.667×105.11 ≈ 36+70.07    | 106.07 |
| 10 | 110         | 0.333×110 + 0.667×106.07 ≈ 36.67+70.71 | 107.38 |

---

### 2. Three Divergence Case Simulations
- Top Divergence: Downtrend Signal
- Bottom Divergence: Uptrend Signal
- Hidden Divergence: Trend Extension Signal
---

### 2.1 **Top Divergence (Bearish Divergence) Example**

Conditions:

* Price makes new high, but EMA5 does not make new high or weakens.

Observe Day 9 and Day 10:

* Price on Day 9 is 108, rises to 110 on Day 10 (new high)
* EMA5 on Day 9 is 106.07, rises to 107.38 on Day 10 (new high, but small increase)

If using a more lenient judgment, assume EMA5 on Day 10 is actually lower than Day 9, or the increase is very limited, indicating weakening EMA momentum.

For example, if Day 10 EMA5 calculates to 106.5 (lower than Day 9), it is a clear top divergence.

Divergence Calculation (assuming EMA Day 10 = 106.5):

```
Price High Difference = 110 - 108 = +2    (New High)
EMA5 High Difference = 106.5 - 106.07 = +0.43    (Very Small Increase, Possibly Weakening)
```

> Top divergence signal implies weakening upward momentum, beware of `possible price pullback or decline`.

---

### 2.2 **Bottom Divergence (Bullish Divergence) Example**

Conditions:

* Price makes new low, but EMA5 does not make new low or strengthens.

Look at Day 2 and Day 3 data:

* Price on Day 2 is 102, falls to 101 on Day 3 (new low)
* EMA5 on Day 2 is 100.67, on Day 3 is 100.79 (rising)

Calculate Divergence:

```
Price Low Difference = 101 - 102 = -1         (New Low)
EMA5 Low Difference = 100.79 - 100.67 = + 0.12    (EMA Rising, Momentum Recovery)
```


> Indicates price making new low, but EMA5 rising, bottom divergence occurs, signaling `weakening downward momentum, possible rebound`.

---

### 2.3 **Hidden Divergence Example**

*The Essence of Hidden Divergence*
- Hidden Divergence = Trend Continuation Signal
- It is not used to judge `turning points`, but to judge `continuation after pullbacks`

#### 2.3.1 Hidden Bullish Case

Conditions:

* Price high does not make new high, but EMA5 high makes new high.

Look at Day 7 and Day 8:

* Price on Day 7 is 106, on Day 8 is 107 (not new high, possibly slight pullback)
* EMA5 on Day 7 is 104.08, on Day 8 is 105.11 (new high)

Judgment:

```
Price High Difference = 107 - 106 = +1   (Not Obvious New High, Can Be Considered Small Pullback)
EMA5 High Difference = 105.11 - 104.08 = +1.03   (EMA New High)
```
##### 🔍 Explanation:
Yes, 107 is indeed higher than 106, which is an `absolute new high` — this is correct in terms of numerical size.  
But saying "not obvious new high" here means:  
+ Although the price slightly made a new high, the amplitude is only +1 (from 106 to 107), `very small increase`;  
+ Compared to previous trends, this increase may not represent "continued upward trend", but rather a `signal of weakening upward momentum`.


##### 🧠 Simple Understanding:
> If prices over consecutive days are: 100 → 105 → 106 → 107, rising slower and slower,  
even if 107 is a new high, but only +1 increase, may represent "weakening upward momentum".


This indicates that although `price did not make new high`, the momentum indicator `(EMA5) strengthens`, trend may `continue upward`, called `hidden bullish divergence`.

#### 2.3.2 Hidden Bearish Case (Similar)

`Price low does not make new low`, but `EMA low makes new low`, implying `downtrend continuation`.

---

### 3. Divergence Percentage Calculation Example

Using Day 10 top divergence as example (assuming EMA Day 10 = 106.5):

* Price vs EMA10 Divergence Calculation Formula:

$$
\text{div} = \frac{\text{Price} - \text{EMA}_{10}}{\text{EMA}_{10}} \times 100\%
$$

* Specific Values:

$$
\text{div} = \frac{110 - 106.5}{106.5} \times 100\% \approx 3.29\%
$$


* Explanation:

Price (110) is about 3.29% higher than EMA10 (106.5). This means the price has far outpaced the moving average, with strong short-term upward momentum.

---

**Divergence Meanings and Risks:**

* If price makes new high (110), but EMA10 does not correspondingly make new high (still 106.5 or lower), it means price rise speed far exceeds the trend momentum reflected by the moving average.

* This "price and moving average divergence" usually means the price has risen too fast in the short term, possibly entering overbought zone.

* **The larger the divergence, the more severe the price deviation from the trend foundation, and the greater the potential adjustment or reversal risk.**

* Especially in top divergence scenarios, if the moving average does not make new high and divergence is obvious, it is often an important signal of insufficient market upward power, possible price pullback or decline.

---

#### Divergence Percentage Summary:

| Divergence Percentage  | Meaning          | Operation Suggestions      |
| ------ | ----------- | --------- |
| Less than 1%   | Price and moving average trends synchronized   | Continue holding or observe   |
| 1%~3% | Price starts deviating from moving average    | Pay attention to risks, observe signals |
| Greater than 3%   | Price far exceeds moving average, obvious divergence | Be cautious about reducing positions, prevent reversals |

> **Tip:** Divergence is only a risk warning signal; combining volume, other indicators, and patterns can improve accuracy.

---



### Divergence Signals Summary

| Divergence Type | Price Performance     | EMA Performance      | Meaning        |
| ---- | -------- | ---------- | --------- |
| Top Divergence  | Price makes new high    | EMA does not make new high or weakens | Upward momentum weakens, risk |
| Bottom Divergence  | Price makes new low    | EMA does not make new low or strengthens | Downward momentum weakens, rebound |
| Hidden Divergence | Price does not make new high/low | EMA makes new high or new low | Trend continuation signal    |

---




## 6. Signal Traps and Counter Strategies

In trend strategies, **EMA / SMA (Exponential / Simple Moving Average) crossover signals** are widely used to judge buy and sell points, but they also often have "signal traps," especially in oscillating markets or extreme conditions, easily leading to **misjudged entries and exits**, even continuous losses. Below we detail these trap types and provide corresponding solutions:

---

### 1. ❌ Frequent Golden/Dead Crosses in Oscillating Markets ("Jitter Trap")

**Manifestation**: Price fluctuates up and down in short periods, causing frequent moving average crossovers, but no real trend forms.  
**Problem**: Continuous entries and exits, accumulating small losses into large ones.

**Schematic:**

```
Price: ─╮╰─╮╰─╮╰── (Oscillation)
SMA10: ╭──╯╭──╯╭── (Frequent Crossovers with SMA30)
```

**Solutions**:

* Add trend confirmation conditions, such as **ADX > 20** or **MACD histogram enlargement**.
* Set time filters (wait N K-lines after signal appears for confirmation) to avoid immediate entry.
* Introduce volatility filters (e.g., ATR / Bollinger Band).

---

### 2. ❌ False Golden/Dead Cross Trap ("False Signal Trap")

**Manifestation**: Golden cross signal appears, but no price volume increase or sustained rise, resulting in quick price reversal downward.  
**Common in**: Near important moving averages (e.g., SMA200), support/resistance levels, news disturbances.

**Solutions**:

* Combine with **volume increase** as auxiliary signal (e.g., volume > average volume * 1.2 during golden cross).
* Combine with **candlestick structures**, such as large bullish candle, engulfing pattern on golden cross day.
* Lag the signal, set **entry buffer zone**, such as closing price higher than crossover point by a certain margin.

---

### 3. ❌ Delay Trap ("Lagging Signal Trap")

**Manifestation**: Golden cross signal appears only after price has risen significantly, entering at this point is already the tail, with high pullback risk.  
**Especially**: SMA signals are slower, EMA slightly faster but still lagged.

**Solutions**:

* Combine price and moving average divergence to filter late signals, e.g., do not enter if EMA10 and EMA50 distance exceeds threshold.
* Introduce **leading indicators** like RSI / Momentum for preliminary judgment, golden cross only for confirmation.
* Try short-term EMA (e.g., EMA5/EMA13) for faster judgment.

---

### 4. ❌ Cutting Losses at Dead Cross Trap ("Premature Profit-Taking Trap")

**Manifestation**: Exit immediately upon dead cross, but price does not truly decline, instead continues rising, leading to missing profits.  
**Cause**: Dead cross does not always mean trend end.

**Solutions**:

* After dead cross, **set delayed exit mechanism**, e.g., confirm price decline after 3 K-lines before exiting.
* Or use **trend judgment profit-taking** mechanisms, e.g., exit only if price breaks EMA30 + RSI<50.
* Use **custom ROI curves** or dynamic profit-taking instead of one-size-fits-all dead cross exit.

---

#### ✅ Summary and Counter Suggestions Table

| Trap Type     | Typical Manifestation    | Cause Analysis      | Counter Strategies                       |
| -------- | ------- | --------- | -------------------------- |
| Frequent Oscillation Crossovers   | Quick multiple entries/exits | No Trend in Market     | Add Trend Confirmation (ADX/MACD), Wait for Confirmation, Multi-Factor Filtering |
| False Golden/Dead Cross | Reversal After Signal  | Lack of Volume / Momentum | Volume Judgment, Candlestick Pattern Verification, Price Confirmation          |
| Delayed Signals     | Late Entry    | Moving Average Lag      | Combine Fast Line Divergence, Price Breakout Confirmation, Fast Period EMA       |
| False Exit at Dead Cross    | Premature Profit-Taking   | Trend Still Continuing    | Delayed Exit After Dead Cross, Dynamic Profit-Taking, Trend Filtering          |

---



## 7. Strategy Example (Freqtrade)
> Disclaimer: This strategy is for learning and exchange purposes only. Please strictly control risks in actual operations; any losses incurred are borne by the individual.
- MA Golden/Dead Cross Signals (Short-term and long-term moving average crossovers)
- Continuous 3 Divergence Signals (Price and short-term MA divergence appears 3 times consecutively to trigger)

```python
from freqtrade.strategy import IStrategy
import talib

class MAStrategy(IStrategy):
    timeframe = '15m'
    stoploss = -0.10
    minimal_roi = {"0": 0.05}

    # Divergence detection window length
    divergence_window = 1

    def populate_indicators(self, dataframe, metadata):
        # Calculate short-term and long-term SMA
        dataframe['sem_10'] = talib.SMA(dataframe['close'], timeperiod=10)
        dataframe['sma_50'] = talib.SMA(dataframe['close'], timeperiod=50)

        # Price and short-term moving average divergence percentage
        dataframe['divergence'] = (dataframe['close'] - dataframe['sem_10']) / dataframe['sem_10'] * 100

        return dataframe

    def _check_divergence(self, dataframe, idx):
        """
        Judge at idx position whether continuous 3 divergence signals appear,
        Simply defined here: Absolute divergence value greater than threshold 1%, continuous 3
        """
        if idx < self.divergence_window - 1:
            return False
        recent_divs = dataframe['divergence'].iloc[idx - (self.divergence_window - 1): idx + 1]
        # Absolute values all greater than 1%
        if all(abs(d) > 1.0 for d in recent_divs):
            return True
        return False

    def populate_entry_trend(self, dataframe, metadata):
        dataframe['enter_long'] = 0

        for i in range(len(dataframe)):
            # Golden cross buy condition: Short-term moving average crosses above long-term from below
            if i == 0:
                continue
            prev_short = dataframe.at[i - 1, 'sem_10']
            prev_long = dataframe.at[i - 1, 'sma_50']
            curr_short = dataframe.at[i, 'sem_10']
            curr_long = dataframe.at[i, 'sma_50']

            golden_cross = prev_short < prev_long and curr_short > curr_long

            # Continuous 3 divergence signals
            divergence_signal = self._check_divergence(dataframe, i)

            if golden_cross and divergence_signal:
                dataframe.at[i, 'enter_long'] = 1

        return dataframe

    def populate_exit_trend(self, dataframe, metadata):
        dataframe['exit_long'] = 0

        for i in range(len(dataframe)):
            if i == 0:
                continue
            prev_short = dataframe.at[i - 1, 'sem_10']
            prev_long = dataframe.at[i - 1, 'sma_50']
            curr_short = dataframe.at[i, 'sem_10']
            curr_long = dataframe.at[i, 'sma_50']

            # Dead cross sell condition: Short-term moving average crosses below long-term from above
            death_cross = prev_short > prev_long and curr_short < curr_long

            if death_cross:
                dataframe.at[i, 'exit_long'] = 1

        return dataframe

```
For strategy running commands, please refer to [Freqtrade Section](../../quants/Freqtrade/002.Common Commands/001.Command Encyclopedia.md)
### Backtest Results for MAStrategy

- **Trades**: 11
- **Average Profit %**: 1.47%
- **Total Profit**: 53.76 USDT (5.38%)
- **Average Duration**: 9 hours 10 minutes
- **Win / Draw / Loss**: 7 / 0 / 4
- **Win Rate**: 63.6%
- **Max Drawdown**: 22.30 USDT (2.12%)

---




## 8. Conclusion and Practical Suggestions

* SMA and EMA are the most basic and important trend indicators, but must be used in combination with market structure and other indicators to avoid mechanical operations.
* Divergence indicators are leading warning signals; reasonably setting thresholds and combining multi-factor confirmation can effectively reduce false signal risks.
* It is recommended to backtest and verify strategy parameters across multiple periods and varieties, combining trend filtering and volume confirmation to enhance live trading stability.

---