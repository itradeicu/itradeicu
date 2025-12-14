# Limit Orders Not Filling, Market Orders Slipping? Understanding Freqtrade’s Order System
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In Freqtrade strategy execution, how orders are placed, how long they remain valid, and whether to use exchange-native stoploss orders directly affect execution efficiency and fund safety. The order system configuration is essentially the “last mile” to stable live trading. This guide explains key parameters with examples.

---

## 📦 `order_types` — Control How Orders Are Placed

```python
order_types = {
    "entry": "limit",      # Entry as limit order
    "exit": "limit",       # Exit as limit order
    "stoploss": "market"   # Stoploss as market order
}
```

**Supported order types:**

* `limit`: Default, posts a limit order waiting for execution. Lower slippage, but may miss opportunities.
* `market`: Executes immediately. Good for fast entries/exits, but may cause slippage.

**Recommended combinations:**

| Scenario       | entry  | exit   | stoploss |
| -------------- | ------ | ------ | -------- |
| Conservative   | limit  | limit  | market   |
| High-frequency | market | market | market   |
| Fast stoploss  | limit  | limit  | market   |

---

## 🕓 `order_time_in_force` — Order Validity Policy

```python
order_time_in_force = {
    "entry": "GTC",
    "exit": "GTC"
}
```

**Meaning:**

* **GTC (Good Till Canceled):** Order remains until filled or canceled by the bot.
* **IOC (Immediate Or Cancel):** Executes as much as possible immediately, cancels remainder.
* **FOK (Fill Or Kill):** Must fully fill immediately, otherwise canceled.

**Suggestions:**

* GTC → best for limit orders (wait until filled).
* IOC/FOK → better for market execution when speed is priority.

---

## 🔒 `stoploss_on_exchange` — Exchange-Level Stoploss Orders

```python
order_types = {
    "stoploss_on_exchange": True,
    "stoploss_on_exchange_interval": 60,
    "stoploss_on_exchange_limit_ratio": 0.99
}
```

**Explanation:**

| Parameter                          | Description                                               |
| ---------------------------------- | --------------------------------------------------------- |
| `stoploss_on_exchange`             | Whether to use exchange-native stoploss (default `False`) |
| `stoploss_on_exchange_interval`    | Frequency (seconds) to check stoploss order status        |
| `stoploss_on_exchange_limit_ratio` | Limit price multiplier for stoploss (Trigger × ratio)     |

**Example:**

* Current price = 100 USDT, stoploss = -5% → trigger price = 95 USDT
* `limit_ratio = 0.99` → stoploss limit order = 95 × 0.99 = **94.05 USDT**

---

## ✅ Why Enable Exchange Stoploss?

| Type           | Depends on bot running  | Speed | Crash Protection | Slippage Control |
| -------------- | ----------------------- | ----- | ---------------- | ---------------- |
| Local Stoploss | Yes                     | Slow  | ❌                | Poor             |
| Exchange Stop  | No (stored at exchange) | Fast  | ✅                | ✅                |

> With exchange stoploss enabled, even if your bot crashes or loses connection, the exchange executes the stoploss automatically — much safer for live trading.

---

## ⚠️ Two Key Concepts in Stoploss Orders

**✅ Trigger Price**

* The condition price at which the stoploss order is placed on the market.
* Example: Stoploss = -5%, entry = 100 → trigger = 95.

**✅ Limit Price**

* The actual order price (controlled by `limit_ratio`).
* Prevents excessive slippage.
* Example: 95 (trigger) × 0.99 = **94.05 (limit)**.

---

## 📌 Example Configuration (Recommended)

```python
order_types = {
    "entry": "limit",
    "exit": "limit",
    "stoploss": "market",
    "stoploss_on_exchange": True,
    "stoploss_on_exchange_interval": 60,
    "stoploss_on_exchange_limit_ratio": 0.985
}

order_time_in_force = {
    "entry": "GTC",
    "exit": "GTC"
}
```

---

## 🧠 Summary Recommendations

| Parameter                          | Description                      | Recommended Setting  |
| ---------------------------------- | -------------------------------- | -------------------- |
| `order_types.entry`                | Entry order type                 | `"limit"` (default)  |
| `order_types.exit`                 | Exit order type                  | `"limit"`            |
| `order_types.stoploss`             | Stoploss order type              | `"market"`           |
| `stoploss_on_exchange`             | Use exchange-native stoploss     | `True` (recommended) |
| `stoploss_on_exchange_limit_ratio` | Distance between trigger & limit | `0.99` \~ `0.985`    |
| `order_time_in_force`              | Order validity policy            | `"GTC"` (default)    |
