**Market Breadth Strategy Engine v2.1** is a fully client-side quantitative research tool designed to analyze **market breadth**, **regime conditions**, and **strategy robustness** across global assets.

It allows users to:
- Fetch real market data (ETF / Crypto)
- Compute multiple breadth definitions
- Execute multiple trading strategies
- Run parameter grid backtests
- Analyze robustness via 4D surfaces
- Evaluate risk-adjusted performance
- Run Monte Carlo forward simulations

> ⚠️ **No backend, no build process, no database.**  
> Runs entirely in the browser using public APIs.

---

## 🚀 Live Demo

```

[https://waranyutrkm.github.io/market-breadth-engine/](https://waranyutrkm.github.io/market-breadth-engine/)

```

(Replace `USERNAME` with your GitHub username)

---

## 🧱 Tech Stack

- **HTML5 / Vanilla JavaScript**
- **TailwindCSS (CDN)**
- **Chart.js** – Equity & Breadth Charts
- **Plotly.js** – 3D Surface & Distribution
- **Math.js** – Statistical Calculations
- **Yahoo Finance API (via CORS proxies)**
- **Binance API (Crypto)**

---

## 📁 Project Structure

```

market-breadth-engine/
│
├── index.html        # Entire application (UI + Engine)
└── README.md         # Documentation

```

---

## 🔁 Step-by-Step Application Workflow

---

### STEP 1 — Asset Universe Selection

The universe defines **what assets are used to calculate market breadth**.

#### Available Modes
- **ETF Mode**
  - SPY, QQQ, IWM, VT, TLT, GLD, etc.
  - Trading Days = **252**
- **Crypto Mode**
  - BTC, ETH, SOL, etc.
  - Trading Days = **365**

Users may:
- Use predefined universe
- Manually add/remove tickers
- Set a **benchmark asset** (default: SPY / BTC)

---

### STEP 2 — Data Fetching

Each asset is fetched independently.

#### ETF Data Source
- Yahoo Finance `chart` endpoint (daily OHLCV)
- Uses **multiple CORS proxies** for reliability

#### Crypto Data Source
- Binance REST API (`/api/v3/klines`)

#### Data Normalization
All assets are aligned to a **common date index**:
- Missing dates → forward-filled
- Ensures synchronized time series

---

## 📊 STEP 3 — Market Breadth Calculation

Market breadth measures **how many assets are bullish** under a chosen rule.

Let:
- `P(t)` = Price today
- `P(t-LB)` = Price at lookback
- `LB` = Lookback period (days)

An asset is **bullish** if:

```

P(t) > P(t - LB)

```

---

### Breadth Weighting Methods

---

### 1️⃣ Equal Weight (Count)

Counts how many assets are bullish.

#### Formula
```

Breadth = (# of bullish assets) / (total assets)

```

#### Example
- Universe: 5 assets  
- Bullish: 3

```

Breadth = 3 / 5 = 0.60 (60%)

```

---

### 2️⃣ Volume Weighted

Weights bullish assets by trading volume.

#### Formula
```

Breadth = Σ(Volume of bullish assets) / Σ(Total volume)

```

#### Example
| Asset | Trend | Volume |
|------|------|-------|
| A | Up | 100 |
| B | Down | 500 |

```

Breadth = 100 / (100 + 500) = 0.167 (16.7%)

```

---

### 3️⃣ Trend Momentum (ROC)

Uses **Rate of Change** as weight.

#### Formula
```

ROC = (P(t) - P(t-LB)) / P(t-LB)
Breadth = Σ(Positive ROC) / Σ(|ROC|)

```

#### Example
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

### 4️⃣ Inverse Volatility

Rewards **stable upward trends**.

#### Volatility
```

σ = StdDev(daily returns over 20 days)
Weight = 1 / σ

```

#### Formula
```

Breadth = Σ(Weight of bullish assets) / Σ(All weights)

```

#### Example
| Asset | Vol | Weight |
|------|----|-------|
| A | 10% | 0.10 |
| B | 50% | 0.02 |

```

Breadth = 0.10 / (0.10 + 0.02) = 83%

```

---

## ♟ STEP 4 — Strategy Execution Logic

Let:
- `B` = Breadth Score (0 → 1)

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
- Breadth = 70% → 70% Asset / 30% Cash

---

### 3️⃣ Breadth Alpha

- Invest **only in bullish assets**
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

If B < 0.2 → Buy (Panic)
Else → Cash

```

---

## 💰 STEP 5 — Portfolio Simulation

---

### Portfolio Variables
- Initial Capital
- Shares per asset
- Cash balance
- Daily NAV

---

### Transaction Fees
- Buy Fee (%)
- Sell Fee (%)

#### Buy Cost
```

Total Cost = BuyAmount × (1 + BuyFee)

```

#### Sell Proceeds
```

Cash = SellAmount × (1 - SellFee)

```

---

### Management Fee (Annual)

Converted to **daily fee**:

```

DailyFee = (AnnualFee / 100) / TradingDays
DailyFeeAmount = NAV × DailyFee

```

---

## 📈 STEP 6 — Performance Metrics

---

### CAGR

```

CAGR = (Final / Initial)^(1 / Years) - 1

```

---

### Sharpe Ratio

```

Sharpe = (Mean(Return - RF)) / StdDev(Return) × √TradingDays

```

---

### Sortino Ratio

Uses **downside deviation only**.

```

Sortino = (Mean(Return - RF)) / DownsideDev × √TradingDays

```

---

### Max Drawdown (MDD)

```

MDD = Min((NAV - PeakNAV) / PeakNAV)

```

---

### Calmar Ratio

```

Calmar = CAGR / |MDD|

```

---

## 🔬 STEP 7 — Parameter Grid Search

Loops through:
- Lookback (LB)
- Rebalance Frequency (RB)
- Strategy
- Weighting Method

Produces **Strategy Matrix Table** with:
- CAGR
- Sharpe
- MDD
- Sortino
- Calmar

---

## 🌐 STEP 8 — 4D Robustness Surface

Axes:
- X → Lookback
- Y → Rebalance
- Z → Performance Metric
- Time → Rolling Horizon

Includes:
- Mean ± 1σ / 2σ planes
- Sensitivity visualization
- Stability analysis

---

## 🛡 STEP 9 — Robustness Scoring (Cluster Stability)

Each parameter point is scored using **Gaussian neighborhood smoothing**.

#### Formula
```

Score = Σ(value × weight) / Σ(weight)
weight = exp(-distance² / (2σ²))

```

Rewards **stable parameter regions**, not sharp peaks.

---

## 🎲 STEP 10 — Monte Carlo Simulation

- Uses log-normal process
- Based on empirical μ and σ

#### Price Path
```

P(t+1) = P(t) × exp((μ - 0.5σ²) + σZ)

```

Where:
- `Z ~ N(0,1)`

Outputs:
- Expected CAGR
- Expected Sharpe
- Expected MDD
- Path dispersion

---

## 📤 STEP 11 — CSV Export

Exports:
```

Date, NAV, Breadth %, Exposure %

```

For further analysis in Python / Excel / R.

---

## ⚠️ Disclaimer

This software is for **educational and research purposes only**.  
It does **not** constitute financial advice or investment recommendations.

Market data may be delayed or incomplete.  
Use at your own risk.

---

## ✅ License

MIT License — free to use, modify, and distribute.

---

## 🧠 Author Notes

Designed for:
- Quant researchers
- Systematic traders
- Market regime analysis
- Portfolio risk diagnostics

Pure frontend by design — **transparent, inspectable, reproducible**.

---
```
