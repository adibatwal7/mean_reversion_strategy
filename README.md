# 📈 Mean Reversion Trading Strategy — AAPL (2022–2024)

> A quantitative finance project implementing a **Z-Score based mean reversion strategy** on Apple stock, with interactive 3D visualization built in Python.

---

## 🧠 Overview

This project applies **statistical mean reversion** to AAPL stock using 2 years of daily price data (Jan 2022 – Dec 2023). The core idea: prices that deviate significantly from their historical average tend to revert back. By quantifying that deviation with a Z-score, we generate systematic **buy and sell signals**.

The strategy is visualized in an **interactive 3D Plotly chart** — mapping Time, Price, and Z-Score simultaneously.

---

## 📐 The Mathematics

### 1. 20-Day Moving Average (MA20)
The "gravitational center" — the price level the stock continuously orbits around.

$$MA_{20}(t) = \frac{1}{20} \sum_{i=0}^{19} Price(t-i)$$

### 2. Rolling Standard Deviation
Measures how widely prices scatter around the moving average.

$$\sigma(t) = \sqrt{\frac{1}{20} \sum_{i=0}^{19} (Price(t-i) - MA_{20})^2}$$

### 3. Z-Score (The Core Signal)
Standardizes how far the current price sits from its mean, in units of standard deviation.

$$Z(t) = \frac{Price(t) - MA_{20}(t)}{\sigma(t)}$$

| Z-Score | Interpretation |
|---------|----------------|
| `Z = 0` | Price is exactly at the moving average |
| `Z > +2` | Price is **overbought** — potential SELL signal |
| `Z < −2` | Price is **oversold** — potential BUY signal |
| `Z > ±3` | Extreme deviation — high-conviction signal |

---

## 📊 Trading Signals

| Signal | Condition | Interpretation | Action |
|--------|-----------|----------------|--------|
| 🟢 **BUY** | `Z-Score < −2` | Price is oversold / undervalued | Enter long position |
| 🔴 **SELL** | `Z-Score > +2` | Price is overbought / overvalued | Exit or short position |

---

## 🗂️ Project Structure

```
mean-reversion-strategy/
│
├── index.ipynb          # Main notebook — data, calculations, visualization
├── README.md            # Project documentation
└── requirements.txt     # Python dependencies
```

---

## ⚙️ Installation & Setup

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/mean-reversion-strategy.git
cd mean-reversion-strategy
```

### 2. Install dependencies
```bash
pip install yfinance pandas plotly notebook
```

### 3. Run the notebook
```bash
jupyter notebook index.ipynb
```

---

## 🔬 How It Works — Code Walkthrough

### Step 1: Download Data
```python
import yfinance as yf

data = yf.download("AAPL", start="2022-01-01", end="2024-01-01", auto_adjust=True)
```

### Step 2: Calculate Indicators
```python
# 20-day moving average
data["MA20"] = data[("Close", "AAPL")].rolling(window=20).mean()

# Rolling standard deviation
data["std"] = data[("Close", "AAPL")].rolling(window=20).std()

# Z-Score
data["zscore"] = (data[("Close", "AAPL")] - data["MA20"]) / data["std"]

# Drop NaN rows from the initial 20-day warmup period
data = data.dropna()
```

### Step 3: Generate Signals
```python
data["signal"] = 0
data.loc[data["zscore"] < -2, "signal"] = 1   # BUY
data.loc[data["zscore"] >  2, "signal"] = -1  # SELL
```

### Step 4: 3D Visualization
```python
import plotly.graph_objects as go

fig = go.Figure(data=[
    go.Scatter3d(
        x=data["time_index"],
        y=data[("Close", "AAPL")],
        z=data["zscore"],
        mode='lines',
        line=dict(width=4, color='blue'),
        name='AAPL Mean Reversion Path'
    )
])

fig.update_layout(
    title="Interactive 3D Mean Reversion Visualization - AAPL (2022-2024)",
    scene=dict(
        xaxis_title='Time (Trading Days)',
        yaxis_title='Stock Price ($)',
        zaxis_title='Z-Score'
    )
)

fig.show()
```

---

## 📈 Key Results

| Metric | Value | Significance |
|--------|-------|--------------|
| **Data Points** | 482 trading days | ~2 years of signal history |
| **Date Range** | Jan 2022 – Dec 2023 | Covers bull & bear regimes |
| **Price Range** | $123.05 – $196.07 | 59% price swing |
| **Z-Score Range** | −3.04 to +2.50 | Multiple extreme signal events |
| **MA Window** | 20 days | ~1 trading month |

---

## 🖥️ 3D Visualization — Axis Guide

The chart maps **three dimensions of information** simultaneously:

- **X-Axis** → Time (trading day index, 0–481)
- **Y-Axis** → AAPL closing price in USD
- **Z-Axis** → Z-Score (statistical deviation from the 20-day mean)

The **blue line** traces the stock's mean reversion path through 3D space.  
The **red dashed line** at Z = 0 represents the mean level — the price always gravitates back here.

---

## 🌍 Real-World Applications

This strategy mirrors techniques used by professional quant traders:

| Application | How This Strategy Applies |
|-------------|--------------------------|
| **Pairs Trading** | Use Z-score to trade the spread between two correlated stocks |
| **Statistical Arbitrage** | Run Z-score signals across a basket of assets simultaneously |
| **Options Pricing** | Mean reversion assumptions underpin implied volatility models |
| **ETF Arbitrage** | Exploit short-term deviations of ETF price from its NAV |
| **Risk Management** | Rolling Z-scores flag when positions are dangerously concentrated |

---

## 🚀 Planned Enhancements

- [ ] **Backtesting PnL Engine** — simulate every signal, record entry/exit prices, calculate total return, win rate & Sharpe ratio
- [ ] **Stop-Loss Logic** — auto-exit if price moves 5% against position (cap losses when mean reversion fails)
- [ ] **Multi-Asset Extension** — apply Z-score framework to a basket of stocks
- [ ] **Transaction Cost Modeling** — include brokerage fees & slippage for realistic net PnL
- [ ] **Regime Detection** — pause strategy during trending (non-mean-reverting) markets

---

## 🛠️ Tech Stack

| Library | Purpose | Key Feature |
|---------|---------|-------------|
| `yfinance` | Download historical OHLCV data | `yf.download()` with auto-adjust |
| `pandas` | Data manipulation & rolling calculations | `.rolling().mean()` / `.std()` |
| `plotly` | Interactive 3D visualization | `go.Scatter3d`, scene layout |
| `jupyter` | Notebook-based development | Cell-by-cell execution & markdown |

---

## 📚 Concepts Covered

- ✅ Moving Averages & Rolling Statistics
- ✅ Z-Score / Statistical Standardization
- ✅ Mean Reversion Theory
- ✅ Quantitative Signal Generation
- ✅ 3D Financial Data Visualization
- ⏳ Backtesting (coming soon)
- ⏳ Risk-Adjusted Returns / Sharpe Ratio (coming soon)
