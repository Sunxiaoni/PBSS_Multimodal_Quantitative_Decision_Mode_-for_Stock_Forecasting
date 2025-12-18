# PBSS: Multimodal Quantitative Decision Model for Stock Forecasting (ongoing)

**PBSS** (Pause–Buy–Sell–Stop) is a **multi-asset machine-learning trading signal system** that integrates:

- Price–volume technical indicators
- FinBERT-based news sentiment analysis
- XGBoost-based return forecasting
- Rule-based PBSS decision logic
- Daily inference pipeline for multiple tickers

This repository provides a reproducible, end-to-end quantitative framework combining time-series modeling with NLP sentiment features, suitable for academic evaluation (e.g., ETH Zürich Quantitative Finance program), research, and applied portfolio analytics.

---

## 🔧 Key Features

### Multimodal Architecture

Integrates independent financial modalities:

- Technical indicators (momentum, trend, volatility)
- Return lags and rolling statistics
- News-derived sentiment signals:
  - `sent_mean_1d`
  - `pos_ratio_7d`
  - `news_count_1d`
  - `sent_mean_3d_ewm`
  - `sent_surprise_1d`

### Multi-Asset Coverage

Supports a main ticker and peer tickers:

```text
AAPL, MSFT, AMZN, GOOGL, META,
TSLA, NVDA, NFLX, AMD, AVGO
```
---

## 🧬 Pipeline Overview

### 1. Market Data Layer
- Incremental OHLCV ingestion
- Stored as Parquet under data/market_raw/
- Ensures reproducibility and avoids redundant downloads
  
### 2. News Sentiment Layer
- News crawling and FinBERT scoring
- Daily aggregation (T+1 alignment)
- Written to:
  
```text
data/sentiment_daily/sentiment_daily.parquet
```

### 3. Feature Engineering
Includes:
- Technical indicators (RSI, EMA/SMA, MACD, ATR, Bollinger Bands)
- Return lags and rolling stats
- NLP-derived sentiment signals
- Target label: next-day return (```text
  target_ret_1d```)
  
### 4. Model Training
- XGBoost regression model per ticker
- Saves:
  - Model file (```text
    *_pbss_xgb.pkl```)
  - Metadata (```text
    *_pbss_meta.json```)
    
### 5. PBSS Decision Logic
Maps forecasts + sentiment into trading actions.
| Condition                                     | PBSS      |
| --------------------------------------------- | --------- |
| Strong positive return & supportive sentiment | **Buy**   |
| Mild positive return                          | **Sell**  |
| Negative return or negative sentiment shock   | **Stop**  |
| Uncertain / low conviction                    | **Pause** |

### 6. Daily Inference
Produces dashboard-ready Parquet files containing:
- Close price
- True return
- Predicted return
- Sentiment features
- PBSS signal
  
---

## 🚀 Quick Start
Install dependencies
```text
pip install -r requirements.txt
```
Update market data
```text
python src/data_market.py
```
Train all PBSS models
```text
python src/train_pbss.py
```
Generate inference outputs
```text
python src/predict_pbss.py
```
Outputs appear in:
```text
data/pbss_signals/
```
---

## 📊 Example PBSS Output
|       Date |  Close | ret_1d | pred_ret | sent_mean_3d_ewm | PBSS  |
| ---------: | -----: | -----: | -------: | ---------------: | :---- |
| 2025-09-08 | 229.40 |  0.004 |    0.012 |             0.03 | Buy   |
| 2025-09-09 | 231.10 | -0.003 |   -0.017 |            -0.24 | Stop  |
| 2025-09-10 | 227.20 |  0.002 |    0.004 |             0.05 | Pause |

---
## 📈 Visualization
### Tableau Desktop & Cloud (to be update)
---

## 🧠 Methodology Summary

### Modeling

- XGBoost regression
- Time-series per ticker
- Multimodal feature integration
  
### Sentiment Features

Extracted from FinBERT-scored news:
- 1-day mean
- 7-day positive ratio
- 3-day EWM
- Sentiment surprise
- News volume
  
### Signal Generation

Return forecasting + sentiment → PBSS trading states.

---

## ✉️ Contact

For questions or collaborations, feel free to reach out: sunhan933@gmail.com
