# 📘 Chapter 7: How to Set Up Freqtrade Web UI – Installation & Usage Guide
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
When running Freqtrade for live trading or backtesting, you may want to **monitor your strategy, positions, and P\&L in real-time**. This is where the **Web UI (FreqUI)** comes in.

This guide covers installing and running the Web UI, both via CLI and Docker, and highlights common usage scenarios.

![Web UI](https://fastly.jsdelivr.net/gh/bucketio/img18@main/2025/07/19/1752932190240-03fecb1c-526c-4711-baa6-cdc43a05ca6a.png)

---

## 🧩 1. Install Web UI Dependencies

Before using the Web UI for the first time, run:

```bash
freqtrade install-ui
```

This command installs all required modules to run the frontend interface.

---

## 🚀 2. Start the Webserver

The Webserver acts as the backend for the UI. It listens on a port (default `8080`):

```bash
freqtrade webserver \
  --config user_data/config.json
```

Access it in your browser:

```text
http://localhost:8080
```

---

## ⚙️ 3. Common Parameters

| Parameter                   | Description                                   |
| --------------------------- | --------------------------------------------- |
| `--config`                  | Path to your config.json                      |
| `--port`                    | Listening port (default: 8080)                |
| `--username` / `--password` | Login credentials                             |
| `--api-server`              | Enable REST API service                       |
| `--webserver`               | Start the UI web service (enabled by default) |

Example with custom port and credentials:

```bash
freqtrade webserver \
  --config user_data/config.json \
  --port 8888 \
  --username admin \
  --password 123456
```

![Web UI Example](https://fastly.jsdelivr.net/gh/bucketio/img15@main/2025/07/19/1752932226796-fee4ad2d-6191-4090-b1bf-7d5e085d1f5f.png)

---

## 🐳 4. Docker Setup Example

When using Docker, ensure the `api_server.listen_port` in `config.json` matches the port mapping in `docker-compose.yml`:

```json
"api_server": {
    "enabled": true,
    "listen_ip_address": "127.0.0.1",
    "listen_port": 7777,
    "verbosity": "error",
    "enable_openapi": false,
    "username": "1",
    "password": "1"
}
```

Docker Compose:

```yaml
services:
  freqtrade:
    image: freqtradeorg/freqtrade:stable
    volumes:
      - ./user_data:/quants/freqtrade/user_data
    ports:
      - "8888:7777" # Access via http://localhost:8888
    command: >
      webserver
      --config /quants/freqtrade/user_data/config.json
      --username admin
      --password 123456
```

Start the container:

```bash
docker compose up -d
```

---

## 📊 6. Web UI Modules

Freqtrade provides **two types of Web UI**, catering to different scenarios:

### ✅ 1. Live/Trading Web UI (`freqtrade trade`)

Used for real-time bot monitoring:

* View active strategy, positions, P\&L
* Manage orders / close positions / manual trades (API must be enabled)
* Browse historical trades and performance charts

Startup:

```bash
freqtrade trade --config user_data/config.json
```

---

### 🧪 2. Backtesting Web UI

Used for **visualizing backtest results**:

* Graphical display of equity curves, indicators, and buy/sell points
* Strategy performance overview and trade statistics
* Requires prior `freqtrade backtesting` run

Startup:

```bash
freqtrade webserver --config user_data/config.json
```

---

## 🔧 7. Common Issues & Tips

| Issue                        | Solution                                                      |
| ---------------------------- | ------------------------------------------------------------- |
| Cannot access UI             | Ensure webserver mode is running; port is exposed             |
| Blank page / broken layout   | Run `freqtrade install-ui` to install frontend dependencies   |
| Cannot access UI in Docker   | Check port mapping in `docker-compose.yml`                    |
| Strategy not recognized      | Ensure filename and `"strategy"` field in `config.json` match |
| Backtesting UI shows no data | Run `backtesting` first, then start `webserver --backtesting` |

---

## 📌 Summary

Freqtrade Web UI is a lightweight, practical interface that allows you to:

* Monitor live trading
* View backtest or live performance
* Manually intervene in trades

If you are comfortable with CLI but want a **visual and intuitive way to manage strategies and assets**, enabling the Web UI is highly recommended.
