# ECON 144 — Economic Forecasting

Time series forecasting projects from ECON 144 (Economic Forecasting) at UCLA. Each project applies econometric forecasting methods to a different macroeconomic or financial series using R.

- **Course:** ECON 144 — Economic Forecasting, UCLA
- **Author:** Manuela Lozano
- **Tools:** R (`forecast`, `fpp3`, `quantmod`, `vars`, `strucchange`, `ggplot2`)
- **Quarter:** Winter 2026

---

## Projects

### Project 1 — Forecasting Monthly U.S. Retail Sales

**Data:** FRED series RSAFS (monthly retail sales and food services, millions of dollars)

**Research question:** How can trend and seasonal components in U.S. retail sales be modeled and removed to produce reliable short-term forecasts, and what dynamics remain after decomposition?

**Methods:**
- ADF unit root test → confirmed non-stationarity
- Linear vs. quadratic trend models (AIC/BIC selection → quadratic wins, Adj R²=0.9664)
- Additive vs. multiplicative seasonal decomposition → multiplicative preferred (seasonal magnitude grows with the series)
- Final model: quadratic trend + seasonal effects → 12-month forecast

**Key findings:**
- Retail sales exhibit strong upward trend + proportional seasonal fluctuations
- Quadratic trend substantially outperforms linear (Adj R²: 0.929 → 0.966)
- Even after decomposition, residuals show significant serial correlation (Box-Ljung p<2.2e-16), suggesting remaining short-run cyclical dynamics suitable for ARIMA modeling

**Files:** `project1-retail-sales/`

---

### Project 2 — U.S. Labor Market Indicators: ARIMA and VAR Forecasting

**Data:** FRED series ICSA (weekly initial jobless claims, aggregated monthly) and TEMPHELPS (monthly temporary help services employment)

**Research question:** Does temporary help services employment contain predictive information about future jobless claims, and does a VAR framework improve short-term forecast accuracy over univariate ARIMA models?

**Methods:**
- STL decomposition of log-transformed series
- Structured ARIMA models with trend, seasonality, and COVID-19 intervention dummy (2020 Mar–Jun)
- Compared to automatic ARIMA via out-of-sample MAPE (12-step holdout)
- Forecast combination (simple average)
- VAR(6) model selected by AIC on first-differenced log series
- Impulse response functions (IRF) and Granger causality tests
- CUSUM stability tests

**Key results:**

| Model | MAPE |
|---|---|
| Structured ARIMA — Jobless Claims | 0.414 |
| Auto ARIMA — Jobless Claims | 0.487 |
| Structured ARIMA — Temp Employment | 0.353 |
| Auto ARIMA — Temp Employment | 0.384 |
| Forecast Combination — Claims | 0.400 |

**Key findings:**
- Structured ARIMA (with economic structure + COVID intervention) outperforms auto ARIMA for both series
- Granger causality runs from jobless claims → temporary employment (p<2.2e-16), not the reverse
- VAR(6) is stable (all roots inside unit circle) but does not improve on univariate ARIMA for short-run forecasting

**Files:** `project2-labor-market/`

---

### Project 3 — Forecasting the USD/COP Exchange Rate

**Data:** FRED series COLCCUSMA02STM (monthly USD/COP exchange rate, 1995–present)

**Research question:** Which time-series forecasting method produces the most accurate out-of-sample forecasts for the USD/COP exchange rate?

**Methods:**
- Log transformation to stabilize variance
- ADF test → non-stationary; first differencing removes persistence
- STL decomposition → trend-dominant, weak seasonality
- Train/test split: pre-2022 training, 2022+ test
- Models: ARIMA, ETS, Holt-Winters, NNETAR, Prophet
- Forecast combination (ARIMA + ETS + NNETAR average)
- Evaluation: RMSE, MAE, MAPE on test set

**Key results:**

| Model | RMSE | MAE | MAPE |
|---|---|---|---|
| **ETS — best model** | **0.0734** | **0.0552** | — |
| ARIMA | higher | higher | — |
| Combination (ARIMA+ETS+NNETAR) | 0.0807 | 0.0585 | — |
| Holt-Winters, NNETAR, Prophet | higher | higher | — |

**Key findings:**
- ETS is the best individual model — outperforms ARIMA, Holt-Winters, NNETAR, and Prophet on all metrics
- Forecast combination improves over most individual models but does not beat ETS alone
- Exchange rate is trend-dominant with minimal seasonal structure

**Files:** `project3-exchange-rate/`

---

## Repository Structure

```
econ144-time-series-forecasting/
├── project1-retail-sales/
│   ├── Project1.Rmd           # Analysis: trend/seasonal modeling + 12-month forecast
│   ├── Project1.pdf           # Full written report
│   └── RSAFS.csv              # FRED retail sales data
├── project2-labor-market/
│   ├── econ144_project2.Rmd   # ARIMA + VAR + Granger causality analysis
│   └── econ144_project2.pdf   # Full written report
└── project3-exchange-rate/
    ├── econ144_project3.Rmd   # Multi-model forecast comparison
    └── econ144_project3.pdf   # Full written report
```

> **Note:** Projects 2 and 3 pull data live from FRED via `quantmod::getSymbols()` — no local data files needed. Project 1 requires `RSAFS.csv` (included).
