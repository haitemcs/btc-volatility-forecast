# BTC Volatility Forecast

Machine learning project for predicting the next-hour BTC volatility ratio from historical market data and engineered features.

## Stack

- Python
- pandas / NumPy
- scikit-learn
- XGBoost
- Jupyter
- FastAPI
- PostgreSQL
- Docker

## Target

Hourly volatility ratio:

    R_t = (High_t - Low_t) / Low_t

The model uses information available before the prediction horizon to estimate the next-hour volatility.

## Pipeline

    Market Data
        ↓
    Cleaning
        ↓
    Feature Engineering
        ↓
    Time-Series Validation
        ↓
    Model Training
        ↓
    Evaluation
        ↓
    FastAPI
        ↓
    PostgreSQL
        ↓
    Docker

## Features

The project evolved from basic OHLCV features to a larger feature set including:

- OHLCV
- rolling and lagged features
- order-flow features
- funding information
- time-based features

## Models

Experiments include:

- Ridge Regression
- Random Forest / tree-based models
- XGBoost
- Neural Network
- HistGradientBoosting

## Evaluation

- MAE
- RMSE
- R²

Model behavior is also evaluated during high-volatility periods rather than relying only on average metrics.

## Important ML lessons

Financial time series require chronological validation. Random train/test splitting can introduce future information into training and produce misleading results.

Another finding was that models can perform reasonably in normal volatility but underestimate extreme movements.

## Engineering

The trained model is exposed through a FastAPI service and packaged with Docker alongside PostgreSQL.

## Status

Baseline forecasting pipeline completed. Current focus is improving tail/high-volatility prediction and making the model pipeline more robust.
