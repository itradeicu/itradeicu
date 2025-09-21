# 🔌 Connecting to Exchanges: Detailed Freqtrade Exchange Configuration
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
Freqtrade connects to exchanges through the `exchange` and `ccxt_config` settings in `config.json`. This step is essential for strategy execution and involves API keys, WebSocket, rate limits, and other critical parameters.

> This article provides a detailed guide on configuring exchange access parameters and recommended practices for a more efficient and stable connection.

---

## 🏦 Exchange Configuration Details

```json
"exchange": {
  "name": "binance",               // Exchange name, e.g., binance, bybit, okx
  "key": "your_api_key",           // Your API Key
  "secret": "your_api_secret",     // API Secret
  "password": "your_password",     // Required for some exchanges like OKX, optional for others
  "ccxt_config": { ... },          // CCXT additional settings (see below)
  "pair_whitelist": [ "BTC/USDT" ],// List of tradable pairs
  "pair_blacklist": [ "*/BNB" ]    // Blacklisted pairs (optional)
}
```

#### 📌 Field Descriptions

| Field            | Description                                                    |
| ---------------- | -------------------------------------------------------------- |
| `name`           | Exchange name in English (must support CCXT)                   |
| `key`            | API key provided by the exchange for trading                   |
| `secret`         | Corresponding secret key                                       |
| `password`       | Additional authentication, used by OKX, Bitget etc. (optional) |
| `pair_whitelist` | Only allow trading for pairs listed here                       |
| `pair_blacklist` | Exclude certain pairs (e.g., low liquidity or unstable coins)  |

#### 🧠 Usage Tips

* API permissions must include both **read + trade** to enable live trading;
* Use separate API keys for different strategies for better isolation and risk control;
* Do not upload your `config.json` to GitHub or other public platforms!

---

### ⚙️ ccxt\_config — Advanced Connection Settings

`ccxt_config` is used when Freqtrade calls the CCXT library, allowing optimization of request frequency, real-time data, market type, etc.

```json
"exchange": {
    "ccxt_config": {
        "enableRateLimit": true          // Enable rate limit to prevent IP ban
    },
    "enable_ws": true,                  // Enable WebSocket for faster real-time data
    "markets_refresh_interval": 60      // Refresh interval for trading pairs in seconds
}
```

---

## ⛓️ enableRateLimit — Enable Rate Limiting (Recommended)

```json
"exchange": {
    "enableRateLimit": true
}
```

* Controls whether Freqtrade automatically respects exchange API rate limits;
* Prevents IP bans or trade restrictions by the exchange;
* Strongly recommended to enable.

---

## 📡 enable\_ws — Enable WebSocket for Real-Time Data

```json
"exchange": {
    "enable_ws": false
}
```

* When enabled, Freqtrade receives real-time market and order updates via WebSocket;
* Faster and lower latency than REST API, suitable for high-frequency strategies;
* Requires exchange support and sometimes additional permissions (e.g., OKX);
* Can be disabled if high-frequency trading is not needed.

---

## 🔁 markets\_refresh\_interval — Market Data Refresh Interval

```json
"exchange": {
    "markets_refresh_interval": 60  // Refresh trading pair list every 60 seconds
}
```

* Interval in seconds;
* Controls how often the trading pairs are updated;
* Default 60 seconds is sufficient for stability;
* Setting it too low may trigger API rate limits.

---

## 🧪 Verify Exchange Connection

After configuration, test if the exchange is connected successfully:

```bash
freqtrade list-markets --config user_data/config.json
```

✅ Returns a list of trading pairs, indicating API key and market type are correctly configured
❌ If you see `authentication error` or `403`, check:

* API Key and Secret are correct
* Trade permissions are granted
* Two-factor authentication does not restrict API usage

---

## ✅ Recommended Configuration Example

```json
"exchange": {
    "name": "binance",
    "key": "your_api_key",
    "secret": "your_api_secret",
    "enable_ws": true,
    "markets_refresh_interval": 60,
    "ccxt_config": {
        "enableRateLimit": true,
        "options": {
            "defaultType": "spot"
        }
    }
}
```

---

## 🧠 Summary Checklist

| Parameter                  | Description                        | Recommended Setting      |
| -------------------------- | ---------------------------------- | ------------------------ |
| `exchange.name`            | Exchange name                      | e.g., `binance`, `bybit` |
| `exchange.key/secret`      | API credentials                    | Required                 |
| `enableRateLimit`          | Limit request rate to avoid risk   | `true`                   |
| `enable_ws`                | Enable WebSocket real-time data    | `true` (if supported)    |
| `markets_refresh_interval` | How often to refresh trading pairs | Typically `60` seconds   |
