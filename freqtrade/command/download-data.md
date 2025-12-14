# 📘 Chapter 3: "How to Download Historical K-Line Data in Crypto? Freqtrade download-data Tutorial"

This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu) for more benefits.&#x20;

In Freqtrade, **K-line data is the foundation for backtesting, optimization, and live trading**. Whether you are a strategy developer or researcher, mastering the `download-data` command is the first step in quantitative trading.

This article explains how to use `freqtrade download-data` to download historical market data, including common parameters, Docker usage, download recommendations, and supported exchanges.

***

## 📥 1. Basic Command Format

1. Download by specifying trading pairs:

```bash
freqtrade download-data \
  --exchange binance \
  --pairs BTC/USDT \
  --timeframes 1h \
  --timerange 20230101-20230701
```

2. Download according to `config.json`:

```bash
freqtrade download-data \
  --config user_data/config.json \
  --timeframes 15m \
  --timerange 20200101-20250626
```

### Common Parameter Explanation

| Parameter      | Description                                                            |
| -------------- | ---------------------------------------------------------------------- |
| `--exchange`   | Choose exchange (e.g., binance, bybit, kucoin)                         |
| `--pairs`      | Specify trading pairs, e.g., `BTC/USDT`, multiple separated by commas  |
| `--timeframes` | Timeframe to download, e.g., `1m`, `15m`, `1h`, `1d`                   |
| `--timerange`  | Data period, format `YYYYMMDD-YYYYMMDD`                                |
| `--days`       | Download the last N days of data (mutually exclusive with `timerange`) |
| `--config`     | (Optional) Use an existing config file to specify exchange             |

***

## 📊 2. Suggestions for Downloading Multiple Coins / Timeframes

1. Multiple pairs (e.g., BTC, ETH, BNB):

```bash
--pairs BTC/USDT,ETH/USDT,BNB/USDT
```

2. Multiple timeframes (e.g., download 15m and 1h simultaneously):

```bash
--timeframes 15m,1h
```

3. Best practice: download each timeframe separately for easier management:

```bash
freqtrade download-data --timeframes 15m
freqtrade download-data --timeframes 1h
```

***

## 🐳 3. Downloading Data in Docker

For Docker deployments, the command differs slightly:

```bash
docker compose run --rm freqtrade download-data \
  --config /quants/freqtrade/user_data/config.json \
  --timeframes 15m \
  --timerange 20220101-20230701
```

Ensure you mount the `user_data/` directory correctly in `docker-compose.yml`:

```yaml
volumes:
  - "./user_data:/quants/freqtrade/user_data"
```

***

## 📂 4. Where Is the Data Stored?

Downloaded K-line data is saved at:

```
user_data/data/<exchange>/<pair><timeframe>-<type>.feather
```

Example:

```
user_data/data/binance/BTC_USDT_USDT-5m-futures.feather
```

Freqtrade automatically recognizes and uses these files for backtesting and optimization.

***

## 🔍 5. Supported Exchanges in Freqtrade

You can run the following command to see supported exchanges:

```bash
freqtrade list-exchanges
```

Common supported exchanges (may vary by version):

* Binance (Spot / Futures)
* Bybit
* KuCoin
* Huobi
* Kraken
* OKX
* Coinbase Pro
* Gate, etc.

> ⚠️ Some exchanges require an API key to download historical data.

***

## ✅ 6. Notes and Recommendations

| Item                   | Recommendation                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------ |
| Download period        | Usually cover at least 3–6 months of data                                                  |
| Time granularity       | For strategy development, `1h` or `15m` is common; for high-frequency strategies, use `1m` |
| Longer historical data | Too long data may mislead due to structural market changes                                 |
| Local storage          | 1m data consumes significant disk space                                                    |

***

## 📌 Summary

`freqtrade download-data` is the starting point for all strategy development. This article covered:

* Parameter usage: exchange / pairs / timeframes / timerange
* Techniques for downloading multiple coins and multiple timeframes
* Docker environment download method
* Data storage path and supported exchanges
