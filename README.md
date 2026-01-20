# 🌍 Market Breadth Strategy Engine v2.1  
**Global Multi-Asset Quantitative Research Platform (Pure Frontend)**

---

## 👤 Author
**GitHub:** waranyutrkm  
**Project Type:** Quantitative Research & Strategy Prototyping  
**Architecture:** 100% Client-Side (No Backend)

---

## 🚀 Live Demo

👉 https://waranyutrkm.github.io/market-breadth-engine/

---

## 📌 Project Overview

**Market Breadth Strategy Engine v2.1** is a fully browser-based quantitative research tool designed to analyze **market breadth**, **market regimes**, and **strategy robustness** across global assets.

The engine allows researchers and traders to:
- Measure market participation (breadth)
- Detect regime shifts
- Test multiple systematic strategies
- Perform parameter grid search
- Evaluate robustness (not just peak performance)
- Simulate forward uncertainty via Monte Carlo

> ⚠️ This project is designed for **research and educational purposes**.  
> It is not a trading bot and does not place real trades.

---

## 🧱 Technology Stack

- **HTML5 / Vanilla JavaScript**
- **TailwindCSS (CDN)**
- **Chart.js** – Equity & Breadth Charts
- **Plotly.js** – 3D Surface, Distribution, Monte Carlo
- **Math.js** – Statistical Calculations
- **Yahoo Finance API** (ETF / Macro Assets via CORS Proxy)
- **Binance REST API** (Crypto Assets)

---

## 📁 Project Structure

```

market-breadth-engine/
│
├── index.html     # Entire application (UI + Quant Engine)
└── README.md      # Documentation

```

No build system, no Node.js, no server required.

---

## 🔁 Full Workflow (Step-by-Step)

---

## STEP 1 — Asset Universe Definition

The **asset universe** defines which instruments are used to compute market breadth.

### Supported Market Modes

### 1️⃣ ETF / Global Macro Mode
- Equities: SPY, QQQ, IWM, VT
- Fixed Income: TLT, IEF, LQD, HYG
- Commodities: GLD, SLV, USO, DBC
- Alternatives: VNQ, UUP  
- **Trading Days / Year:** 252

### 2️⃣ Crypto Mode
- BTC, ETH, SOL, BNB, etc.
- **Trading Days / Year:** 365

Users may:
- Add or remove assets manually
- Choose a benchmark asset (default: SPY / BTC)

---

## STEP 2 — Market Data Fetching

Each asset is fetched independently.

### ETF Data
- Source: Yahoo Finance `chart` API
- Interval: Daily
- Range: ~5 years
- CORS handled via multiple fallback proxies

### Crypto Data
- Source: Binance `/api/v3/klines`
- Interval: 1 day

### Data Alignment
All assets are synchronized to a **common date index**:
- Missing data → forward-filled
- Ensures consistent cross-asset calculations

---

## STEP 3 — Market Breadth Calculation

Market breadth measures **how many assets are participating in an uptrend**.

Let:
- `P(t)` = Price at time `t`
- `LB` = Lookback period (days)

An asset is **bullish** if:

```

P(t) > P(t - LB)

```

---

## STEP 4 — Breadth Weighting Methods

---

### 1️⃣ Equal Weight (Count-Based)

Counts how many assets are bullish.

**Formula**
```

Breadth = Number of Bullish Assets / Total Assets

```

**Example**
- 5 assets
- 3 are bullish

```

Breadth = 3 / 5 = 0.60 (60%)

```

---

### 2️⃣ Volume Weighted

Weights bullish assets by trading volume.

**Formula**
```

Breadth = Σ(Volume of Bullish Assets) / Σ(Total Volume)

```

**Example**

| Asset | Trend | Volume |
|------|------|--------|
| A | Up | 100 |
| B | Down | 500 |

```

Breadth = 100 / (100 + 500) = 0.167 (16.7%)

```

Interpretation: Large assets selling can dominate the market.

---

### 3️⃣ Trend Momentum (ROC Weighted)

Uses **Rate of Change (ROC)** as weight.

**Formula**
```

ROC = (P(t) - P(t-LB)) / P(t-LB)
Breadth = Σ(Positive ROC) / Σ(|ROC|)

```

**Example**

| Asset | ROC |
|------|----|
| A | +10% |
| B | -2% |
| C | +5% |

```

Numerator = 10% + 5% = 15%
Denominator = |10| + |2| + |5| = 17%
Breadth = 15 / 17 = 0.88 (88%)

```

---

### 4️⃣ Inverse Volatility (Risk-Adjusted)

Rewards **stable trends** over volatile ones.

**Volatility**
```

σ = StdDev(daily returns over N days)
Weight = 1 / σ

```

**Breadth Formula**
```

Breadth = Σ(Weight of Bullish Assets) / Σ(All Weights)

```

**Example**

| Asset | Volatility | Weight |
|------|-----------|--------|
| A | 10% | 0.10 |
| B | 50% | 0.02 |

```

Breadth = 0.10 / (0.10 + 0.02) = 83%

```

---

## STEP 5 — Strategy Execution Logic

Let:
- `B` = Breadth score (0 → 1)

---

### 1️⃣ Breadth Switch
```

If B > 0.5 → 100% Invested
Else → 100% Cash

```

---

### 2️⃣ Breadth Scale
```

Exposure = B

```

Example:
- Breadth = 70% → 70% invested

---

### 3️⃣ Breadth Alpha
- Invest only in bullish assets
- Equal-weight among bullish assets

---

### 4️⃣ Breadth Regime
```

B > 0.6 → 100%
0.4 ≤ B ≤ 0.6 → 50%
B < 0.4 → 0%

```

---

### 5️⃣ Breadth Contrarian
```

If B < 0.2 → Buy (Panic Condition)
Else → Stay in Cash

```

---

## STEP 6 — Portfolio Simulation Engine

---

### Portfolio State Variables
- Cash balance
- Asset shares
- Net Asset Value (NAV)

---

### Transaction Fees

**Buy**
```

Total Cost = BuyAmount × (1 + BuyFee)

```

**Sell**
```

Cash Received = SellAmount × (1 - SellFee)

```

---

### Management Fee

Annual fee converted to daily:

```

DailyFee = AnnualFee / TradingDays
DailyFeeAmount = NAV × DailyFee

```

---

## STEP 7 — Performance Metrics

---

### CAGR
```

CAGR = (Final NAV / Initial NAV)^(1 / Years) - 1

```

---

### Sharpe Ratio
```

Sharpe = Mean(Return − RiskFreeRate) / StdDev(Return) × √TradingDays

```

---

### Sortino Ratio
```

Sortino = Mean(Return − RF) / DownsideDeviation × √TradingDays

```

---

### Maximum Drawdown (MDD)
```

MDD = min((NAV − PeakNAV) / PeakNAV)

```

---

### Calmar Ratio
```

Calmar = CAGR / |MDD|

```

---

## STEP 8 — Parameter Grid Search

The engine iterates over:
- Lookback periods (LB)
- Rebalance frequencies (RB)
- Strategies
- Weighting methods

Results are stored in a **Strategy Matrix Table**.

---

## STEP 9 — 4D Robustness Surface

Dimensions:
- X → Lookback
- Y → Rebalance
- Z → Performance Metric
- Time → Rolling Horizon

Includes:
- Mean
- ±1σ and ±2σ stability planes

Purpose: **Identify stable regions, not sharp peaks**.

---

## STEP 10 — Robustness Scoring (Cluster Stability)

Each parameter point is scored using **Gaussian neighborhood smoothing**.

**Formula**
```

Score = Σ(Value × Weight) / Σ(Weight)
Weight = exp(−distance² / (2σ²))

```

Stable clusters score higher than isolated optima.

---

## STEP 11 — Monte Carlo Simulation

Uses log-normal price dynamics.

**Price Path**
```

P(t+1) = P(t) × exp((μ − 0.5σ²) + σZ)

```

Where:
- `Z ~ N(0,1)`

Outputs:
- Expected CAGR
- Expected Sharpe
- Expected Drawdown
- Distribution of future paths

---

## STEP 12 — CSV Export

Exports:
```

Date, Portfolio NAV, Breadth %, Exposure %

```

For further analysis in Python, R, or Excel.

---

## ⚠️ Disclaimer

This software is provided **for educational and research purposes only**.  
It does not constitute financial advice or investment recommendations.

Market data may be delayed or incomplete.  
Use at your own risk.

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

## 🧠 Design Philosophy

- Transparency over black-box models
- Robustness over overfitting
- Regime awareness over static assumptions
- Research-first, execution-agnostic
