# Can't Even Draw Charts but Claim to Know Strategies? Full Freqtrade `plot_config` Guide
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In Freqtrade strategy development, **visualizing charts** is one of the most intuitive ways to understand signals and debug strategies.
With the `plot_config` parameter, you can clearly display indicators, buy/sell signals, stop-loss points, exit reasons, etc., greatly improving strategy debugging efficiency.

> This article provides a detailed guide on using `plot_config` and common configuration scenarios, helping you master chart-based strategy debugging quickly!

---

## 🧩 What is `plot_config`?

`plot_config` is a dictionary in your strategy class. When used with CLI tools such as:

```bash
freqtrade backtesting --plot
freqtrade plot-dataframe
```

It automatically generates charts showing your buy/sell signals, technical indicators, take-profit/stop-loss points, etc.

✅ Common fields:

| Field Name         | Type | Description                                                         |
| ------------------ | ---- | ------------------------------------------------------------------- |
| `main_plot`        | list | Indicators to plot on the main chart (e.g., EMA, MA)                |
| `subplots`         | dict | Subplots like RSI, MACD, displayed below main chart                 |
| `plot_signals`     | bool | Whether to show buy/sell arrows on the chart                        |
| `plot_trades`      | bool | Whether to show connecting lines between trades                     |
| `plot_exit_reason` | bool | Whether to mark exit reasons (take profit, stop-loss, signal, etc.) |

---

## 🔧 Example Configuration

```python
plot_config = {
    'main_plot': ['ema_20', 'ema_50'],  # Draw two EMAs on the main chart
    'subplots': {
        "rsi": {
            'rsi': {'color': 'blue'}   # Display RSI in the RSI subplot
        },
        "macd": {
            'macd': {'color': 'green'},
            'macdsignal': {'color': 'orange'}
        }
    },
    'plot_signals': True,         # Show buy/sell arrows
    'plot_trades': True,          # Show trade connecting lines
    'plot_exit_reason': True      # Mark exit reasons
}
```

---

## 📈 Example Chart Explanation

Assume your strategy uses the following indicators:

* EMA20 and EMA50 to determine trends
* RSI for overbought/oversold conditions
* MACD for momentum
* Buy when RSI < 30, sell when RSI > 70

With `plot_config`:

* The main chart shows EMA20 and EMA50
* RSI curve is displayed in a subplot with color
* MACD and its signal line are plotted below
* Buy/sell arrows are shown on the chart
* Trade connecting lines and exit reasons are displayed

---

## 🔍 Debugging Recommendations

| Goal                         | Recommended Configuration                                     |
| ---------------------------- | ------------------------------------------------------------- |
| Check trend indicators       | Add MA, EMA, Bollinger Bands to `main_plot`                   |
| Adjust buy/sell logic        | Enable `plot_signals` and `plot_trades` to view entries/exits |
| Verify exit reason validity  | Enable `plot_exit_reason` to see stop-loss/profit triggers    |
| Validate multiple indicators | Use `subplots` for RSI, MACD, CCI intersection analysis       |

---

## 🚀 How to Generate Charts

To make `plot_config` effective, run commands with the `--plot` parameter:

#### ✅ 1. Plot during backtesting

```bash
freqtrade backtesting --strategy YourStrategy --plot
```

* Automatically opens interactive charts after backtest (or generates charts in Jupyter)
* Shows candles, indicators, signals, and trade paths

#### ✅ 2. Plot indicators for a specific pair (no trades)

```bash
freqtrade plot-dataframe --strategy YourStrategy --pair BTC/USDT --timerange=20220101-20220131
```

* Shows only content from `populate_indicators()`
* Does not rely on entries/exits, ideal for indicator debugging

#### ✅ 3. Export charts as interactive HTML

```bash
freqtrade backtesting --strategy YourStrategy --plot --export-html
```

* Generates interactive HTML files for local viewing or sharing

### 💡 Tips

| Scenario                                     | Recommended Command                              |
| -------------------------------------------- | ------------------------------------------------ |
| Quickly review strategy execution            | `freqtrade backtesting --plot`                   |
| View indicators for a specific pair and time | `plot-dataframe` with `--pair` and `--timerange` |
| Save charts for review or sharing            | Add `--export-html` to generate interactive HTML |

---

## ⚠️ Notes

* Field names in `main_plot` and `subplots` **must match** those defined in `populate_indicators()`
* Chart commands do not automatically save charts; use `--export` or `--plot`
* Too many indicators may clutter the chart; choose selectively

---

## 🧠 Summary

| Parameter          | Controls              | Required | Recommended          |
| ------------------ | --------------------- | -------- | -------------------- |
| `main_plot`        | Main chart indicators | ✅        | EMA, Bollinger Bands |
| `subplots`         | Subplot indicators    | ✅        | RSI, MACD            |
| `plot_signals`     | Show buy/sell arrows  | ✅        | True                 |
| `plot_trades`      | Show trade lines      | ✅        | True                 |
| `plot_exit_reason` | Show exit reason tags | ✅        | True                 |

With `plot_config`, you can **visualize your strategy’s behavior**, making post-development debugging and optimization much easier.
Your strategy logic becomes transparent, turning “blind coding” into informed strategy development.
