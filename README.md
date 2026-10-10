# Bitcoin Volatility Forecasting

A notebook-based machine-learning project exploring whether historical Bitcoin market data and engineered features can help estimate short-horizon volatility. The current repository contains the forecasting notebook; it should not be mistaken for a deployed API service.

## Data

The notebook downloads hourly BTC/USDT candlestick data from the Binance public market-data API. The saved notebook output records 79,789 hourly rows, spanning from August 2017 through September 2026 in that run. Data availability can change, so rerunning the notebook may produce a different dataset.

The candlestick data includes open, high, low, close, volume, and related exchange fields.

## Target

The notebook investigates an hourly range-based volatility measure:

```text
range_t = (high_t - low_t) / low_t
```

For forecasting, features must be restricted to information available before the target hour. This makes chronological splitting and leakage checks important.

## Workflow

1. Download hourly market data.
2. Clean and prepare the time series.
3. Create lagged and rolling features.
4. Define the target and forecasting horizon.
5. Train and compare candidate models.
6. Evaluate predictions using appropriate regression metrics.
7. Inspect failure cases, especially during sharp market moves.

## Tools

The notebook uses Python, pandas, NumPy, requests, Matplotlib, scikit-learn, and PyTorch.

## Run the notebook

Requirements: Python and Jupyter Notebook or JupyterLab.

```bash
python -m venv .venv
```

Activate the virtual environment and install the libraries imported by the notebook. For example:

```bash
python -m pip install jupyter pandas numpy requests matplotlib scikit-learn torch
jupyter notebook
```

Open [Volatility Forecasting.ipynb](Volatility%20Forecasting.ipynb) and run cells in order. The data-download cells make HTTP requests to Binance; access to that endpoint and its current availability are required to fetch fresh data.

## Evaluation notes

For financial time series, use chronological train/validation/test periods rather than randomly mixing future and past observations. Compare against simple baselines, inspect MAE and RMSE alongside R², and analyze performance during high-volatility periods instead of relying only on aggregate scores.

## Current scope

The tracked repository currently contains the notebook and README. A packaged inference service, API, database integration, Docker deployment, and automated CI are not included in the current tracked files.

## License

Distributed under the MIT License. See [LICENSE](LICENSE).
