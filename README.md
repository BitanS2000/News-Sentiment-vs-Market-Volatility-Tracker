# News-Sentiment-vs-Market-Volatility-Tracker
# News Sentiment vs Market Volatility Tracker

Tracks daily news sentiment against market volatility for a portfolio of AI-sector stocks, to explore whether news sentiment correlates with (or leads) price volatility.

## Overview

This project builds an end-to-end pipeline that:

1. Pulls historical price/volume data for a set of AI-related tickers
2. Scrapes recent news headlines for each ticker
3. Scores each headline's sentiment using a finance-tuned NLP model
4. Merges sentiment and market data into a single daily dataset
5. Analyzes and visualizes the relationship between news sentiment, returns, and volatility

**Tickers covered:** NVDA, AMD, MSFT, TSM (grouped by category — Hardware/Chips, Software/Cloud, Infrastructure)

## Pipeline

### 1. Stock Data
- Historical OHLCV data pulled via `yfinance`
- Computes daily returns and 5-day rolling volatility per ticker
- Consolidated into a single structured dataframe (`master_stock_df`)

### 2. News Data
- Headlines and summaries pulled per ticker via the [Finnhub](https://finnhub.io/) API (`company_news` endpoint)
- Collected over a configurable date range

### 3. Sentiment Scoring
- Headlines scored using **FinBERT** (`ProsusAI/finbert`) via HuggingFace `transformers`
- Sentiment converted to a signed score (positive/negative/neutral) and aggregated into a daily average per ticker

### 4. Merged Analysis
- Daily sentiment joined onto stock data on ticker + date
- Correlation matrix computed across `Daily_Return`, `Rolling_Volatility`, and `Daily_Avg_Sentiment`
- A sentiment intensity/conviction metric derived from sentiment magnitude and volume of news

### 5. Visualization
- Dual-axis time series chart: rolling volatility vs. daily average sentiment
- Candlestick + volume chart with sentiment events overlaid (event-study style), built with `mplfinance`

## Example Output

![Volatility vs Sentiment](path/to/chart.png)
*TSM: 5-day rolling volatility (purple) vs. daily average FinBERT sentiment score (red)*

## Tech Stack

- `pandas`, `yfinance` — data collection and processing
- `finnhub-python` — news headline retrieval
- `transformers` (FinBERT) — sentiment scoring
- `matplotlib`, `seaborn`, `mplfinance` — visualization

## Usage

Run the notebook cells in order:
1. Fetch and process stock data
2. Fetch news headlines
3. Run sentiment scoring
4. Merge datasets and generate visualizations

## Key Findings

- Same-day correlation between news sentiment and returns/volatility is weak — expected, since markets don't always react same-day to news
- Some of the sharpest sentiment swings don't correspond to volatility spikes, and vice versa
- Next step: testing whether sentiment **leads** volatility with a 1–2 day lag, rather than assuming a same-day relationship

## Limitations

- News coverage and volume vary by ticker and day, which can bias daily sentiment averages
- Free-tier API rate limits constrain how much historical news data can be pulled
- FinBERT is tuned on financial text, but still an imperfect proxy for market-moving sentiment
