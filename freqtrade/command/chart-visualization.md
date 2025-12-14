# 📘 Chapter 6: "Show and Tell! plot-dataframe Chart Visualization Tutorial"

This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu) for more benefits.&#x20;

During strategy development, backtesting results are often presented in tabular form, making it difficult to quickly identify issues. The `plot-dataframe` command can visualize buy/sell points, indicator lines, and price movements in charts, helping you instantly see strategy performance.

***

## 🎯 1. Basic Usage: Plotting Charts

```bash
freqtrade plot-dataframe \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timerange 20230101-20230201
```

After execution, an `.html` file will be generated under `user_data/plot/`, which can be opened directly in a browser. It includes:

* Candlestick price movements
* Buy/Sell points (arrows)
* Technical indicators (e.g., EMA, MACD)

***

## 🧾 2. Parameter Details

| Parameter          | Description                                                     |
| ------------------ | --------------------------------------------------------------- |
| `--config`         | Path to the config file, including trading pairs and timeframes |
| `--strategy`       | Strategy class name (e.g., `MyStrategy`)                        |
| `--timerange`      | Specify the plotting period, format like `20230101-20230201`    |
| `--indicators1`    | Indicators plotted on the main chart (e.g., EMA, close)         |
| `--indicators2`    | Sub-chart indicators (e.g., RSI, MACD)                          |
| `--exportfilename` | Export file path (supports `.html` or `.png`)                   |
| `--userdir`        | Custom `user_data` path (default is fine)                       |

***

## 📐 3. Adding Custom Indicators

You can add extra indicators to the chart to validate signal logic:

Example code:

```python
def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe['ema'] = ta.EMA(dataframe['close'], timeperiod=20)
    dataframe['fast_ema'] = ta.EMA(dataframe['close'], timeperiod=10)
    dataframe['slow_ema'] = ta.EMA(dataframe['close'], timeperiod=50)
    dataframe['rsi'] = ta.RSI(dataframe['close'], timeperiod=14)
    macd, macdsignal, macdhist = ta.MACD(dataframe['close'])
    dataframe['macd'] = macd
    return dataframe
```

* `--indicators1` will be plotted on the main chart (price chart), e.g., EMA lines.
* `--indicators2` will be plotted on sub-charts, e.g., RSI, MACD.

Full example:

```bash
freqtrade plot-dataframe \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timerange 20230101-20230201 \
  --indicators1 close ema fast_ema slow_ema \
  --indicators2 rsi macd
```

#### ❗ Note:

These indicator names must match the column names in your strategy class’s `populate_indicators()` method (added to the `DataFrame`).

* Names must exactly match your dataframe column names.
* Otherwise, no error will occur, but the chart will not display the desired lines.
* Indicators must be defined in `populate_indicators()` to take effect.

***

## 💾 4. Export as HTML / PNG

By default, output is an HTML file. To specify:

```bash
--exportfilename user_data/plot/myplot.html
```

To export as PNG (static image):

```bash
--exportfilename user_data/plot/myplot.png
```

> 📌 Note: Exporting PNG requires additional tools like Puppeteer or headless Chrome. Beginners are recommended to use HTML format.

***

## 🐳 5. Running in Docker

Example command in Docker:

```bash
docker compose run --rm freqtrade plot-dataframe \
  --config /quants/freqtrade/user_data/config.json \
  --strategy MyStrategy \
  --timerange 20230101-20230201
```

Make sure the `/quants/freqtrade/user_data` directory is correctly mounted in `docker-compose.yml`.

***

## ✅ 6. Usage Suggestions

| Purpose                        | Method                                              |
| ------------------------------ | --------------------------------------------------- |
| Check strategy logic           | See if buy/sell points are at appropriate positions |
| Assist debugging               | Compare indicators with signal relationships        |
| Share strategy                 | Export chart as HTML for easy presentation          |
| Evaluate indicator performance | Plot multiple indicators to check redundancy        |

***

## 📌 Summary

`plot-dataframe` is a powerful visualization tool provided by Freqtrade, especially useful for debugging complex strategies and validating buy/sell logic.

#### Recommended Workflow:

1. Backtest the strategy:

```bash
freqtrade backtesting \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timeframe 15m \
  --timerange 20220101-20230101
```

2. Plot the chart:

```bash
freqtrade plot-dataframe \
  --config user_data/config.json \
  --strategy MyStrategy \
  --timerange 20250601-20250626
```

3. Analyze the chart:

* Are strategy buys too early or too late?
* Are there frequent false signals?
* Are the indicators effective?

Mastering `plot-dataframe` provides a solid foundation for strategy optimization and more efficient tuning!
