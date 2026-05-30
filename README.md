# 📈 Stock Market Return Prediction Using R

> **Forecasting S&P 500 & FTSE All-Share indices across 30 years of market data using five time series models — evaluated across three economic eras including the 2008 financial crisis and COVID-19 onset**

---

## 🔍 Project Overview

This project investigates the **long-term predictability of major stock market indices** by benchmarking five classical forecasting methodologies against 30 years of historical price data (January 1990 – January 2020). The study focuses on the **S&P 500 Composite** and **FTSE All-Share Price Index**, evaluating each model's accuracy across three distinct time horizons designed to capture different market regimes: the dot-com boom, the 2008 financial crisis, and the post-recovery bull market.

The core research question: *Can traditional statistical models reliably predict equity index returns, and under what conditions do they break down?*

---

## 📊 Key Results at a Glance

| Model | Best RMSE (S&P 500) | Best MAPE | Period |
|---|---|---|---|
| ARIMA | **82.26** | **6.37%** | 2000 |
| SES | 184.24 | 4.99% | 2020 |
| Moving Average (2OMA) | 253.21 | 8.04% | 2020 |
| Simple Forecast (Drift) | 254.93 | 17.41% | 2000 |
| Simple Forecast (Mean) | — | — | Baseline |

**Key finding:** ARIMA delivered the strongest performance, achieving an RMSE of 82.26 and MAPE of just 6.37% on S&P 500 data for the 1990–2000 period. All models, however, failed to anticipate the 2008 financial crisis — confirming that conventional time series methods cannot model black swan events.

---

## 🧠 Methodology

### Data Source
- **Package:** `tsfe::indices` (R) — a curated dataset of global stock indices and exchange rates
- **Indices used:** S&P 500 Composite (column 13) and FTSE All-Share Price Index (column 5)
- **Period:** January 1, 1990 – January 30, 2020 (~7,844 daily observations, aggregated to monthly)
- **Preprocessing:** Log returns calculated; data aggregated to monthly means; three train/test splits created

### Three Time Horizons
Data was split into three training windows, each forecasting the next **24 months**:

| Horizon | Training Data | Forecast Period |
|---|---|---|
| 1 | Jan 1990 – Dec 1997 | 1998–1999 |
| 2 | Jan 1990 – Dec 2009 | 2010–2011 |
| 3 | Jan 1990 – Dec 2018 | 2019–2020 |

This design captures three distinct economic regimes: the 1990s bull market, the post-GFC recovery, and the pre-pandemic expansion.

---

## 🔬 Models Implemented

### 1. Simple Forecasts (Mean, Naïve, Drift)
Baseline benchmarks. The **Drift method** (random walk with trend) consistently outperformed Mean and Naïve across both indices by incorporating directional momentum. None of the three methods could capture the 2008 downturn or COVID-19 onset.

### 2. Moving Average (2nd-Order)
A 2OMA was applied using the `ma()` function from the `forecast` package. While it effectively smoothed noise, it introduced lag — a structural limitation that results in delayed responses to sharp market reversals. RMSE ranged from 253 (S&P, 2020) to 591 (FTSE, 2010).

### 3. Simple Exponential Smoothing (SES)
SES assigned exponentially decreasing weights to historical observations, making it more adaptive than simple moving averages. It achieved the lowest MAPE of 4.99% for S&P 500 in the 2020 horizon, outperforming simpler methods in stable trend environments.

### 4. ARIMA (Auto-selected via AIC)
`auto.arima()` with AIC-based model selection was used for all six training sets. ARIMA achieved the best overall accuracy, with the lowest AIC of **775.26** for S&P 500 (1990–2000). Residual diagnostics (ACF, PACF, histogram, density plots) confirmed near-random residuals for this model, indicating a well-specified fit.

> ARIMA accuracy results:
> - S&P 500 (2000): RMSE = 82.26, MAE = 73.68, MAPE = 6.37%, AIC = 775.26
> - FTSE (2000): RMSE = 262.42, MAE = 235.57, MAPE = 9.16%, AIC = 1000.31
> - S&P 500 (2020): RMSE = 146.01, MAE = 115.56, MAPE = 3.98%, AIC = 3435.23

### 5. Seasonal-Trend Decomposition (STL)
STL decomposition separated each series into trend, seasonal, and remainder components. Findings confirmed a **persistent upward long-term trend** in both indices, with a **consistent annual seasonal pattern** (±10–20 index points in S&P) that proved resilient even through the 2008 crisis. Remainder forecasts were modelled using ARIMA on the residual component.

---

## 📉 Statistical Analysis

### Log Returns Summary

| Index | N | Mean Daily Return | Std Dev | Min | Max |
|---|---|---|---|---|---|
| S&P 500 | 7,844 | 0.00028 | 0.011 | -0.095 | +0.110 |
| FTSE All-Share | 7,844 | 0.00016 | 0.0099 | -0.087 | +0.088 |

Both distributions exhibit **leptokurtosis** (fat tails), confirming that returns are not normally distributed — a critical consideration for any risk model assuming Gaussian behaviour.

### Accuracy Metrics Used
- **RMSE** — Root Mean Square Error (penalises large deviations heavily)
- **MAE** — Mean Absolute Error (average prediction error in index points)
- **MAPE** — Mean Absolute Percentage Error (scale-independent accuracy %)
- **AIC** — Akaike Information Criterion (model fit vs. complexity trade-off)

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **R 4.2** | Core programming language |
| `forecast` | ARIMA, SES, Moving Average, STL forecasting |
| `tsfe` | Source dataset (global equity indices) |
| `ggplot2` | All visualisations and forecast plots |
| `tidyverse` | Data manipulation pipeline |
| `lubridate` | Date handling and time series indexing |
| `ggfortify` | Time series autoplot extensions |
| `Metrics` | RMSE, MAE, MAPE calculation |
| `kableExtra` | Formatted accuracy tables |
| `PerformanceAnalytics` | Financial analytics support |
| `quantmod` | Financial data utilities |
| `fpp2` | Forecasting Principles and Practice framework |
| `gt` | Publication-quality tables |
| RMarkdown / Posit Cloud | Reproducible analysis and reporting |

---

## 📁 Repository Structure

```
STOCK-MARKET-RETURN-PREDICTION-USING-R/
│
├── Advanced_Financial_Data_Analytics_Assignment.html   # Full rendered analysis with all plots
├── Advanced_Financial_Data_Analytics_Assignment.Rmd    # Source R Markdown file
└── README.md
```

---

## 🚀 How to Reproduce

```r
# Install required packages
install.packages(c("forecast", "ggplot2", "tidyverse", "lubridate",
                   "ggfortify", "Metrics", "kableExtra", "fpp2", 
                   "PerformanceAnalytics", "quantmod", "gt", "remotes"))

# Install the TSFE data package from GitHub
remotes::install_github("barryquinn1/tsfe")

# Open and knit the .Rmd file in RStudio or Posit Cloud
# All data loads automatically from tsfe::indices — no external data files needed
```

---

## 💡 Key Insights & Conclusions

1. **ARIMA is the best classical model** for stable trend periods — it achieved up to 93.6% accuracy (MAPE 6.37%) on S&P 500 data from the 1990s bull market.

2. **All models fail during structural breaks.** The 2008 financial crisis and the 2020 COVID-19 shock exposed a fundamental limitation: backward-looking statistical models cannot anticipate black swan events driven by exogenous shocks.

3. **Leptokurtosis is pervasive.** Both indices show fat-tailed return distributions, meaning models that assume normality will systematically underestimate the probability of extreme price movements.

4. **Seasonal patterns are robust.** STL decomposition revealed consistent annual seasonality in both indices that persisted across all three economic regimes — a finding with potential applications in tactical asset allocation.

5. **Longer training data ≠ better MAPE.** The 2020-horizon ARIMA models achieved the lowest MAPE values (3.98% for S&P 500) despite higher absolute RMSE, partly because higher index price levels arithmetically compress percentage errors.

### Future Enhancements
- Integrate macroeconomic indicators (VIX, yield curves, PMI) as exogenous variables in ARIMAX models
- Apply GARCH models to capture volatility clustering in residuals
- Explore LSTM neural networks for non-linear pattern recognition
- Incorporate sentiment data from financial news for event-driven forecasting

---

## 📚 References

- Adebiyi, A.A., Adewumi, A.O., & Ayo, C.K. (2014). Stock Price Prediction Using the ARIMA Model. *16th International Conference on Computer Modelling and Simulation*, pp. 106–112.
- Da, Z., Engelberg, J., & Gao, P. (2011). In search of attention. *Journal of Finance*, 66(5), 1461–1499.
- Kara, Y., Boyacioglu, M.A., & Baykan, Ö.K. (2011). Predicting direction of stock price index movement using ANNs and SVMs. *Expert Systems with Applications*, 38(5), 5311–5319.

---

## 👨‍💻 Author

**Siddharth Shekhar Singh**  
Data & AI Automation Analyst | NI Water, Belfast  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/siddharth-shekhar-singh)

---

*This project was completed as part of an Advanced Financial Data Analytics assignment. The full rendered HTML report with all visualisations is available in the repository.*
