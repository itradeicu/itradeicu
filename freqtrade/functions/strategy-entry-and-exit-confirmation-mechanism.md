# 💹 Don’t Just Trade on Signals! Use `confirm_trade_entry` to Avoid Losing Buys
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In quantitative strategies, generating buy or sell signals is only the first step.
Actual trade execution requires multiple checks.
Freqtrade provides two key functions:

* `confirm_trade_entry`
* `confirm_trade_exit`

They act as a “final confirmation” after a strategy signals a trade, deciding whether the trade should actually be executed. Think of them as a **safety valve**, preventing losses caused by signal misjudgment, abnormal market conditions, or position risks.

---

## 📈 1. confirm\_trade\_entry: The Ultimate Filter for Buy Signals

### Function

`confirm_trade_entry` is called **after a buy signal is generated** but **before the actual order is placed**.
It receives information about the current trading environment and returns whether the trade is confirmed.

Main use cases:

* **Risk control**: e.g., prevent buying at too high a price or exceeding max positions
* **Signal re-validation**: confirm buy logic with stricter conditions
* **Dynamic strategy adjustment**: refuse buying based on capital status or market conditions

### Parameters

```python
def confirm_trade_entry(self, pair: str, trade: Trade, order_type: str, amount: float, price: float,
                        current_time: datetime, **kwargs) -> bool:
    """
    pair: trading pair, e.g., BTC/USDT
    trade: current trade object, includes open price, status, etc.
    order_type: limit or market
    amount: proposed buy quantity
    price: proposed buy price
    current_time: current timestamp
    kwargs: other optional parameters
    Returns: True to allow buy, False to reject
    """
```

---

## 📉 2. confirm\_trade\_exit: The Final Gate for Sell Signals

### Function

`confirm_trade_exit` is called **after a sell signal is generated** but **before the order is executed**.

Typical uses:

* **Avoid blind stop-loss**: e.g., only allow selling when profitable
* **Filter abnormal market moves**: e.g., pause selling during flash drops
* **Capital management**: e.g., restrict sell time windows to avoid frequent trading

### Parameters

```python
def confirm_trade_exit(self, pair: str, trade: Trade, order_type: str, amount: float, price: float,
                       current_time: datetime, **kwargs) -> bool:
    """
    Same parameters as confirm_trade_entry
    Returns: True to allow sell, False to reject
    """
```

---

## 📊 3. Code Examples: From Theory to Practice

### 3.1 Limit Buy: Prevent Buying at High Prices

```python
def confirm_trade_entry(self, pair, trade, order_type, amount, price, current_time, **kwargs) -> bool:
    # ma20 = self.dp.get_indicator(pair, 'sma', timeframe='1h', length=20).iloc[-1]
    if price > ma20 * 1.5:
        self.log(f"Reject buy {pair}, price {price} exceeds 1.5x MA20 {ma20 * 1.5}")
        return False
    return True
```

> **Explanation**: Restricts buy price to no more than 1.5× the 20-hour SMA to avoid chasing highs.

---

### 3.2 Sell Only When Profitable: Prevent Loss Locking

```python
def confirm_trade_exit(self, pair, trade, order_type, amount, price, current_time, **kwargs) -> bool:
    if price <= trade.open_rate:
        self.log(f"Reject sell {pair}, price {price} is not higher than open rate {trade.open_rate}")
        return False
    return True
```

> **Explanation**: Only allows selling when the current price is higher than the open price, avoiding losses.

---

### 3.3 Limit Maximum Open Trades: Control Risk Exposure

```python
def confirm_trade_entry(self, pair, trade, order_type, amount, price, current_time, **kwargs) -> bool:
    open_trades = len(self.wallets.get_trades_open())
    max_positions = 3
    if open_trades >= max_positions:
        self.log(f"Reject buy {pair}, open trades {open_trades} exceed max allowed {max_positions}")
        return False
    return True
```

> **Explanation**: Limits the number of simultaneous positions to 3 to avoid over-diversification or liquidation risk.

---

## ⚙️ 4. Recommendations and Notes

* **Use multidimensional data**: Combine volatility, volume, and capital usage for comprehensive decision-making.
* **Detailed logging**: Record every rejected trade for debugging and optimization.
* **High efficiency**: Functions are called frequently; ensure low-latency execution to prevent strategy delays.
* **Cautious with `False`**: Over-rejecting trades may hurt strategy performance; allow some flexibility.

---

## 🚀 5. Summary

`confirm_trade_entry` and `confirm_trade_exit` are indispensable **safety guards** in Freqtrade strategies.

They allow your strategy to **double-check signals** before executing trades, filtering out inappropriate buy or sell actions, and significantly improving safety and stability.

Applied wisely, these functions make your strategy more resilient in real trading, supporting long-term profitability.

---

> Try these functions now and tighten the safety net of your strategy!
