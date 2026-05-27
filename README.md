# 🏛️ Order Block Detection Alert — `Order_Block_Detection_001.mq4`

> **MQL4 Script for MetaTrader 4**  
> Identifies institutional order blocks (demand/supply zones) from significant price action candles and fires real-time alerts when price retests those zones.

---

## Overview

Order blocks are price zones where large institutional participants — banks, hedge funds, and market makers — placed significant buy or sell orders, leaving a visible footprint on the chart. These zones frequently act as strong support and resistance in the future, offering high-probability reversal or continuation setups.

This script scans historical candles within a configurable lookback window, identifies order blocks based on **large-body candle criteria**, and then monitors live price action for retests of those zones, firing alerts the moment price re-enters the block.

---

## How It Works

**Step 1 — Order Block Detection**

For each bar in the lookback window, the script evaluates:

```
bodySize = |close - open|
candleRange = high - low

If bodySize > 60% of candleRange → candle qualifies as an order block candle
```

The aggregate high and low of all qualifying candles define the **order block zone** (`highOB` / `lowOB`).

**Step 2 — Retest Monitoring**

Every 60 seconds the script checks whether the current closing price falls within the zone (with a configurable buffer):

```
currentPrice >= (lowOB - RetestBuffer) AND currentPrice <= (highOB + RetestBuffer)
→ Alert: "Price Retested Order Block Zone"
```

---

## Input Parameters

| Parameter | Default | Type | Description |
|---|---|---|---|
| `TradeSymbol` | `"EURUSD"` | string | Symbol to analyze |
| `Timeframe` | `PERIOD_H1` | ENUM_TIMEFRAMES | Timeframe for order block detection |
| `LookbackPeriod` | `20` | int | Number of bars to scan for order blocks |
| `ZoneWidth` | `5` | int | Visual zone extension in bars (cosmetic) |
| `RetestBuffer` | `0.0002` | double | Price tolerance for zone retest detection (in price units) |
| `EnableAlerts` | `true` | bool | Trigger MT4 sound alerts |
| `EnableEmail` | `false` | bool | Send email notifications |
| `EnablePush` | `false` | bool | Send push notifications to mobile |

---

## Alert Signal

When price retests the detected order block zone:

```
Price Retested Order Block Zone detected on EURUSD (Timeframe: PERIOD_H1)
Price: 1.08432, Zone High: 1.08650, Zone Low: 1.08200
```

All alerts are also logged to the MT4 **Experts journal**.

---

## Installation

1. Copy `Order_Block_Detection_001.mq4` to:
   ```
   MetaTrader 4/MQL4/Scripts/
   ```
2. Restart MT4 or right-click **Navigator** → **Refresh**
3. Drag the script onto the chart of the instrument you want to monitor
4. Configure parameters and click **OK**

---

## Parameter Tuning Guide

| Goal | Recommendation |
|---|---|
| Catch more (smaller) order blocks | Lower `LookbackPeriod` (10–15 bars) |
| Focus on high-impact zones only | Raise `LookbackPeriod` (30–50 bars) |
| Reduce false retest triggers | Lower `RetestBuffer` (0.0001) |
| Allow slightly early alerts | Raise `RetestBuffer` (0.0003–0.0005) |
| Higher timeframe analysis | Switch `Timeframe` to H4 or D1 |

---

## Smart Money / ICT Context

Order blocks are a core concept in **Smart Money Concepts (SMC)** and **Inner Circle Trader (ICT)** methodology. The key principles this script captures:

- **Bullish Order Block** — Last bearish candle before a strong bullish displacement; becomes a demand zone
- **Bearish Order Block** — Last bullish candle before a strong bearish displacement; becomes a supply zone
- **Retest** — Price returns to the zone offering an entry in the direction of the original displacement

For best results, combine with:
- Break of Structure (BOS) confirmation
- Fibonacci levels
- Session time analysis (London/NY open)

---

## Requirements

- MetaTrader 4 (Build 600+)
- `#property strict` compliance (enforced)
- At least `LookbackPeriod + 1` bars of history loaded

---

## Disclaimer

This script is provided for **educational and informational purposes only**. Order block detection is pattern-based and does not guarantee future price reaction at any zone. Always validate signals with additional confluence. Test on a demo account before live use.

---

## License

MIT License — free to use, modify, and distribute with attribution.
