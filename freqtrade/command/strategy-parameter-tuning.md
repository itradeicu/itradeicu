# 📘 Chapter 5: "How to Optimize Strategy Parameters? Freqtrade Hyperopt Quick Start"

This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu) for more benefits.

&#x20;In strategy development, beyond constructing buy/sell logic, **parameter settings often determine the final profit and risk ratio**.

Freqtrade provides a powerful `hyperopt` function for **automated search of optimal parameter combinations**, greatly accelerating strategy iteration.

***

## 🧠 1. What is Hyperopt and When to Use It?

Hyperopt is an **automated parameter optimization tool** that can:

* Find the best RSI threshold
* Test optimal take-profit and stop-loss levels
* Automatically try multiple parameter combinations → compare results → find the best configuration

✅ Suitable scenarios:

* Strategies with multiple numerical parameters (e.g., RSI, MACD, Bollinger Band width, stop-loss ratio)
* Searching for combinations that perform best over historical periods
* Avoiding manual parameter tuning

***

## 🚻 2. Dependency Installation

Hyperopt requires additional modules:

```bash
pip install 'freqtrade[hyperopt]'
```

### Tip

For full functionality including freqai or telegram support:

```bash
pip install 'freqtrade[all]'
```

***

## 🚀 3. Basic Command and Parameters

```bash
freqtrade hyperopt \
  --config user_data/config.json \
  --strategy MyStrategy \
  --hyperopt-loss SharpeHyperOptLoss \
  --timerange 20230101-20230701 \
  --epochs 100
```

| Parameter         | Description                                             |
| ----------------- | ------------------------------------------------------- |
| `--config`        | Configuration file path                                 |
| `--strategy`      | Strategy class to optimize                              |
| `--hyperopt-loss` | Optimization target function                            |
| `--timerange`     | Backtest time range                                     |
| `--epochs`        | Number of iterations (more = more precise but slower)   |
| `--spaces`        | Which parameter spaces to optimize (default: buy, sell) |

***

## 🎯 4. Common Hyperopt Loss Functions

| Function Name             | Meaning                     | Scenario                          |
| ------------------------- | --------------------------- | --------------------------------- |
| `SharpeHyperOptLoss`      | Optimize Sharpe ratio       | Balance risk and return           |
| `SortinoHyperOptLoss`     | Optimize Sortino ratio      | Focus on downside risk            |
| `ProfitHyperOptLoss`      | Maximize total profit       | Aggressive return-driven strategy |
| `CalmarHyperOptLoss`      | Return / max drawdown ratio | Risk-aware preference             |
| `TrailingBuyHyperOptLoss` | For trailing buy strategies |                                   |

***

## 🧩 5. Defining Hyperparameters

Use the `@parameter` decorators in your strategy class:

```python
from freqtrade.strategy import IStrategy, IntParameter, DecimalParameter

class MyStrategy(IStrategy):
    rsi_period = IntParameter(10, 30, default=14, space="buy")
    stoploss_value = DecimalParameter(-0.10, -0.01, default=-0.05, space="sell")
```

Freqtrade will automatically search for the best combination within the defined ranges.

***

## ⚠️ 6. Common Pitfalls

### ❗ Overfitting

Short time frames or single-scenario data may lead to strategies that only perform well historically but fail live.

Recommendations:

* Use longer time periods (6+ months)
* Run Hyperopt multiple times → compare parameter convergence
* Reserve part of data for forward testing

### ❗ Improper Loss Function

Optimizing only for profit may ignore risk, creating extreme strategies.

Recommendations:

* Prefer Sharpe or Sortino as default
* For low risk tolerance, use CalmarHyperOptLoss

### ❗ Large Search Space / Parameter Conflicts

Too many combinations increase search time and may cause conflicts.

Recommendations:

* Limit parameters to 3–6
* Use effective ranges (e.g., RSI from 10–50 instead of 1–100)

***

## 🛠️ 7. Example Strategy and Hyperopt Run

Example strategy:

```python
from freqtrade.strategy.interface import IStrategy
from pandas import DataFrame
from freqtrade.strategy import IntParameter, DecimalParameter
import talib.abstract as ta

class MySimpleStrategy(IStrategy):
    timeframe = '15m'
    rsi_buy = IntParameter(10, 50, default=30, space="buy")
    stoploss_value = DecimalParameter(-0.1, -0.01, default=-0.05, space="sell")
    stoploss = -0.05

    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)
        return dataframe

    def populate_buy_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe.loc[(dataframe['rsi'] < self.rsi_buy.value), 'buy'] = 1
        return dataframe

    def populate_sell_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        dataframe['sell'] = 0
        return dataframe
```

Local run:

```bash
freqtrade hyperopt \
  --config user_data/config.json \
  --strategy MySimpleStrategy \
  --hyperopt-loss SharpeHyperOptLoss \
  --timerange 20230101-20230630 \
  --epochs 100
```

Docker run:

```bash
docker compose run --rm freqtrade hyperopt \
  --config /quants/freqtrade/user_data/config.json \
  --strategy MyStrategy \
  --hyperopt-loss SharpeHyperOptLoss \
  --timerange 20230101-20230630
```

***

### 🎯 Adjustable Parameters

| Parameter        | Type                           | Description       |
| ---------------- | ------------------------------ | ----------------- |
| `rsi_buy`        | `IntParameter(10,50)`          | RSI buy threshold |
| `stoploss_value` | `DecimalParameter(-0.1,-0.01)` | Stop-loss ratio   |

### Tips

1. Use `.value` to access actual parameter value
2. `space="buy"` / `"sell"` defines Hyperopt search space
3. `default=...` is the manually set default
4. Run backtesting first to confirm basic strategy logic

***

## 📊 8. Hyperopt Evaluation Metrics

Common metrics:

| Metric               | Description                             |
| -------------------- | --------------------------------------- |
| `Sharpe Ratio`       | Annualized return / volatility          |
| `Sortino Ratio`      | Annualized return / downside volatility |
| `Calmar Ratio`       | Annualized return / max drawdown        |
| `Total Profit`       | Total profit                            |
| `Drawdown`           | Max drawdown                            |
| `Avg Trade Duration` | Average trade duration                  |

You can also define custom evaluation functions.

***

## ✅ 9. Recommended Workflow

```
1. Use new-strategy to define strategy and parameters
2. Run backtesting to validate basic logic
3. Use hyperopt for automated parameter tuning
4. Replace strategy parameters with optimal values
5. Run backtesting again to verify
6. Visualize results → decide whether to go live
```

***

## 📌 Summary

Freqtrade Hyperopt provides powerful support for parameter tuning, but **correctly defining parameter ranges, loss functions, and data periods is key**.

📍 The goal is **not maximum profit**, but **stable, risk-resistant, and generalizable parameters**.
