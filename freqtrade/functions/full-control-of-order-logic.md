# 📘 Still Blocked by Orders? Try This Freqtrade Smart Order System
> This article was produced by the Quantitative Trading Lab at [https://www.itrade.icu](https://www.itrade.icu). Visit for more benefits.
In quantitative trading, order price and order timeout are key factors that determine trading efficiency and risk management.
The Freqtrade bot provides several custom hooks to finely control buy and sell prices and the order lifecycle, preventing opportunity loss caused by unfavorable order execution or locked funds.

This article focuses on three functions:

* `custom_entry_price`: Customize the buy order price to avoid orders being instantly filled or excessive cost.
* `check_entry_timeout`: Determine whether a buy order has timed out and cancel unfilled orders to release funds.
* `check_exit_timeout`: Determine whether a sell order has timed out to prevent long-standing inactive orders.

---

## 1. Custom Buy Order Price — `custom_entry_price`

In Freqtrade, the default buy price is determined by the exchange's market price or the strategy's default limit price. However, precise control of order price is crucial in practice.

If the order price is too conservative, the buy may not execute quickly, missing good opportunities; if too high, the cost increases.

The `custom_entry_price` function allows you to customize the limit order price, making the buying logic more flexible.

### Use Cases

* **Aggressive strategy**: Use market or near-market price to ensure fast execution.
* **Conservative strategy**: Set a slightly lower price for a better buying cost.
* **Hybrid strategy**: Dynamically adjust price according to market volatility or other signals.

### Example Code

```python
def custom_entry_price(self, pair, current_time, proposed_rate, entry_tag, **kwargs) -> float:
    """
    Customize buy order price

    Parameters:
    - pair: Trading pair, e.g., 'BTC/USDT'
    - current_time: Current timestamp
    - proposed_rate: System suggested price
    - entry_tag: Trade tag for strategy differentiation
    - kwargs: Additional parameters

    Returns:
    - float: Custom buy order price
    """

    if entry_tag == "aggressive":
        return proposed_rate
    elif entry_tag == "conservative":
        return proposed_rate * 0.995
    else:
        return proposed_rate * 0.998
```

### Summary

This allows the strategy to adjust buy prices according to market conditions and capital management needs, effectively preventing instant fills or high costs.

---

## 2. Order Timeout Handling — `check_entry_timeout` & `check_exit_timeout`

During limit orders, the biggest concern is orders being "stuck," occupying funds without executing.

This not only affects capital turnover but may also miss better trading opportunities.

Freqtrade allows developers to define custom timeout rules through `check_entry_timeout` and `check_exit_timeout`.

### Design Idea

* **High-priced coins** (e.g., BTC/USDT): sensitive to capital, cancel if unfilled for a short period.
* **Mid-priced coins** (e.g., BNB, LTC): moderate timeout.
* **Low-priced coins** (e.g., SHIB, DOGE): longer timeout allowed due to volatility.

### Example Code

```python
from datetime import timedelta

def check_entry_timeout(self, pair: str, trade: Trade, order: Order,
                        current_time: datetime, **kwargs) -> bool:
    """
    Determine if a buy order has timed out and should be canceled.

    Timeout logic:
    - Price > 100 USDT, cancel after 5 minutes
    - Price between 10~100 USDT, cancel after 3 minutes
    - Price < 1 USDT, cancel after 24 hours

    Returns:
    - True: cancel order
    - False: continue waiting
    """

    if trade.open_rate > 100 and trade.open_date_utc < current_time - timedelta(minutes=5):
        return True
    elif trade.open_rate > 10 and trade.open_date_utc < current_time - timedelta(minutes=3):
        return True
    elif trade.open_rate < 1 and trade.open_date_utc < current_time - timedelta(hours=24):
        return True
    return False


def check_exit_timeout(self, pair: str, trade: Trade, order: Order,
                       current_time: datetime, **kwargs) -> bool:
    """
    Determine if a sell order has timed out and should be canceled.

    Logic is the same as entry timeout, with different thresholds based on price.
    """
    if trade.open_rate > 100 and trade.open_date_utc < current_time - timedelta(minutes=5):
        return True
    elif trade.open_rate > 10 and trade.open_date_utc < current_time - timedelta(minutes=3):
        return True
    elif trade.open_rate < 1 and trade.open_date_utc < current_time - timedelta(hours=24):
        return True
    return False
```

### Further Optimization

* Adjust timeout dynamically according to market volatility.
* Use order book depth or volume to decide whether to cancel.
* Set different timeout rules for different strategies or coins.

---

## 3. Notes: Limit Order Configuration

`custom_entry_price` and the timeout functions only work with **limit orders**.
Market or other order types will not trigger these functions.

### Enable Limit Orders

In `config.json`:

```json
"order_types": {
  "entry": "limit",
  "exit": "limit"
}
```

Freqtrade will then use limit orders for buy/sell, ensuring these custom logics take effect.

---

## Summary

Order price and timeout management are key to improving trade execution quality.

* Use `custom_entry_price` to customize buy prices according to market conditions and strategy style.
* Use `check_entry_timeout` and `check_exit_timeout` to prevent stuck orders and free up capital.
* With limit order configuration, these three form a complete order management system.

Proper order management prevents instant fills, long-standing orders, or missed better prices, making trading more stable and efficient.
