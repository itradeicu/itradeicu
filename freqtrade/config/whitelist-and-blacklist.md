# 📘 Chapter 7: Whitelist and Blacklist Mechanism — Trade Only the Coins You Want
This article is produced by [https://www.itrade.icu](https://www.itrade.icu) Quantitative Trading Lab. Please visit [https://www.itrade.icu](https://www.itrade.icu)  for more benefits.
In the crypto market, trading pairs are abundant, but their quality varies greatly. Freqtrade provides the `pair_whitelist` and `pair_blacklist` mechanisms, allowing you to **precisely select the coins you want to trade**. This helps avoid trading “low-quality” or low-liquidity coins and improves strategy stability and safety.

These two parameters are flexible and practical, making them essential tools for every quantitative trader.

---

## 🎯 One-Sentence Principle

| Parameter        | Function                                                                                   |
| ---------------- | ------------------------------------------------------------------------------------------ |
| `pair_whitelist` | **Only allow** trading of these pairs; all others are ignored                              |
| `pair_blacklist` | **Prohibit** trading of these pairs; all others are allowed (unless excluded by whitelist) |

> ✅ If both are configured: Tradable pairs = `pair_whitelist` − `pair_blacklist`
> ❗ Ensure the pair format matches the exchange (e.g., `"BTC/USDT"`).

---

## 🧩 Configuration Structure

```json
"exchange": {
  "pair_whitelist": [
    "BTC/USDT",
    "ETH/USDT",
    "SOL/USDT"
  ],
  "pair_blacklist": [
    "DOGE/USDT",
    "LUNC/USDT"
  ]
}
```

| Field            | Type  | Description                                                      |
| ---------------- | ----- | ---------------------------------------------------------------- |
| `pair_whitelist` | Array | List of allowed trading pairs                                    |
| `pair_blacklist` | Array | List of prohibited trading pairs; can be combined with whitelist |

---

## ✅ Common Use Cases

### ✅ 1. Precisely Control Trading Pairs (Trade Only Core Coins)

If you only want to trade major coins like Bitcoin or Ethereum:

```json
"pair_whitelist": [
  "BTC/USDT",
  "ETH/USDT"
]
```

🔒 All other pairs will be ignored, ensuring the strategy focuses on highly liquid and widely accepted assets.

---

### ✅ 2. Exclude Specific High-Risk Pairs

If you want to trade multiple coins but exclude volatile or problematic ones:

```json
"pair_blacklist": [
  "SHIB/USDT",
  "LUNA/USDT"
]
```

✅ This prevents accidental exposure to high-risk coins.

---

### ✅ 3. Dynamic Management with Automated Filtering

You can dynamically generate the whitelist or blacklist via external scripts based on:

* 24-hour trading volume ranking
* Market capitalization ranking
* Time since listing
* Regulatory risk, etc.

This enables **smart coin selection + blacklist exclusion** as a combined risk-control approach.

---

## 🔁 Whitelist vs Blacklist: Detailed Comparison

| Feature              | pair\_whitelist                                                | pair\_blacklist                                                             |
| -------------------- | -------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Control Method       | Only allow listed pairs                                        | Exclude listed pairs; others are tradable                                   |
| Purpose              | Select high-quality targets, limit strategy scope              | Broad trading while excluding risky coins                                   |
| Flexibility          | Low; must manually list all pairs                              | High; only maintain pairs to exclude                                        |
| Recommended Scenario | Conservative strategies; backtesting aligned with live trading | Automated selection strategies or momentum strategies needing wide coverage |

---

## 📌 Combined Usage Recommendation

You can use both whitelist and blacklist for finer control:

```json
"pair_whitelist": [
  "BTC/USDT",
  "ETH/USDT",
  "XRP/USDT"
],
"pair_blacklist": [
  "XRP/USDT"
]
```

👆 Final tradable pairs: **BTC/USDT, ETH/USDT**
❗ Coins in the blacklist (XRP/USDT) are excluded even if they appear in the whitelist.

---

## 💡 Practical Tips & Techniques

1. **Combine Dynamic Management**

   * Whitelist: Use automated scripts to fetch top N coins by volume for trading
   * Blacklist: Manually maintain long-term problem coins (e.g., low liquidity, abandoned projects)

2. **Regular Review**

   * Check lists weekly or monthly to ensure they match current market conditions

3. **Combine with Position Limits**

   * If whitelist is broad, use `max_open_trades` to control open positions

4. **Multi-Strategy Isolation**

   * Each strategy can have its own coin range to avoid interference (recommend separate config.json files)

---

## 🧠 Summary Checklist

| Parameter        | Description                             | Recommended Practice                                                    |
| ---------------- | --------------------------------------- | ----------------------------------------------------------------------- |
| `pair_whitelist` | Clearly allow specific trading pairs    | Select high-quality pairs consistent with strategy targets              |
| `pair_blacklist` | Clearly prohibit specific trading pairs | Exclude risky, volatile, or low-liquidity coins                         |
| Combined Use     | whitelist − blacklist                   | Precisely control core targets while avoiding temporary high-risk coins |

---

By combining `pair_whitelist` and `pair_blacklist`, Freqtrade allows **precise coin-level trading control**, improving strategy stability and preventing losses due to low-quality pairs.

📌 Next time, no more excuses like “the strategy lost because it traded a weird coin.” Start with proper configuration!
