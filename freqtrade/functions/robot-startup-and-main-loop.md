# 📘 Get Ahead! Freqtrade Strategy “Startup + Loop” Dual Core, Trading Bot Mastery Guide!
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In the Freqtrade strategy framework, `bot_start()` and `bot_loop_start()` are two important lifecycle callback functions. They allow developers to insert custom logic at the bot’s startup and at the start of each main loop.
Understanding and properly using these two functions can help you control the trading bot more flexibly, improving strategy performance and extensibility.

---

## ⚙️  bot\_start() - Bot Startup

### Purpose

* Called once after the bot starts and completes initialization.
* Suitable for placing logic that needs to run at startup, such as:

  * Preloading data
  * Initializing custom resources or services
  * Sending startup notifications
  * Printing welcome messages or logs

### Notes

* Triggered only once at startup
* Should not block for too long to avoid affecting startup efficiency

### Example Code

```python
def bot_start(self, **kwargs):
    """
    Called once when the bot starts.
    """
    print("[bot_start] Bot starting, initializing resources...")
    # Example: load additional data
    self.custom_data = self.load_custom_data()
    print("[bot_start] Custom data loaded.")

def load_custom_data(self):
    # Example custom data loading function
    return {"key": "value"}
```

---

## ⚙️ bot\_loop\_start() - Bot Loop Execution

### Purpose

* Called at the start of each main trading loop (i.e., at the beginning of each new event loop iteration)
* Suitable for:

  * Refreshing status at the start of each loop
  * Monitoring or logging
  * Dynamically adjusting parameters
  * Periodically calling external APIs

### Notes

* May be called multiple times, so logic should be lightweight to avoid blocking
* Can be used to dynamically adjust strategy configuration at runtime

### Example Code

```python
def bot_loop_start(self, **kwargs):
    """
    Called at the start of each main trading loop.
    """
    print("[bot_loop_start] New trading loop started.")
    # Example: dynamically refresh a status
    self.dynamic_factor = self.calculate_dynamic_factor()

def calculate_dynamic_factor(self):
    # Pseudo-code: calculate a dynamic factor
    return 42
```

---

## 🔍 Combined Example: Practical Use Case

```python
class MyStrategy(IStrategy):
    def bot_start(self, **kwargs):
        print("[bot_start] Strategy starting, loading configuration.")
        self.trade_count = 0

    def bot_loop_start(self, **kwargs):
        print("[bot_loop_start] New loop started, trades completed:", self.trade_count)
        # Clean or refresh status at the start of each loop
        self.refresh_market_data()

    def refresh_market_data(self):
        # Simulate data refresh
        print("[bot_loop_start] Market data refreshed.")

    def populate_entry_trend(self, dataframe, metadata):
        # Simple example entry signal
        dataframe['enter_long'] = dataframe['close'] > dataframe['open']
        return dataframe

    def populate_exit_trend(self, dataframe, metadata):
        dataframe['exit_long'] = dataframe['close'] < dataframe['open']
        return dataframe

    def order_filled(self, trade, order):
        self.trade_count += 1
        print(f"[order_filled] Amount filled: {order.amount_filled}")
```

---

## 🧠 Summary

| Callback Function  | Trigger Timing                        | Typical Use Case                                        | Notes                                   |
| ------------------ | ------------------------------------- | ------------------------------------------------------- | --------------------------------------- |
| `bot_start()`      | Called once after bot startup         | Initialize data, custom resources, send notifications   | Should be quick, non-blocking           |
| `bot_loop_start()` | Called at the start of each main loop | Refresh state dynamically, logging, fetch external data | Called multiple times, keep lightweight |

---

By properly using `bot_start()` and `bot_loop_start()`, you can insert custom logic at key lifecycle points of a Freqtrade trading bot, enhancing strategy flexibility and runtime efficiency.
These two functions are core components of the strategy callback interface, and mastering them can effectively support the development and debugging of complex trading strategies.
