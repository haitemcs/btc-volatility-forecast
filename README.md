# BTC Volatility Forecasting

Machine learning project for predicting the next-hour BTC volatility ratio.

## Models

Neural Network  
Ridge Regression  
XGBoost

## Target

The target is the next-hour volatility ratio.

Hourly range:

R_t = (High_t - Low_t) / Low_t

The model uses historical market information and engineered features to estimate future volatility.

## Results

The models perform reasonably in normal volatility conditions.

During high-volatility periods, actual values can reach around 3–4 while predictions often remain around 1.5–2.

Neural Network: more variation and higher predictions, but less stable.

Ridge: smoother and more conservative predictions.

XGBoost: good in the normal range, but tends to underestimate extreme volatility.

## Why?

MSE:

MSE = (1/n) Σ(y_i - ŷ_i)²

Extreme volatility events are rare, so normal observations dominate the loss.

The target is also imbalanced across volatility regimes. There are many normal periods and relatively few extreme events.

XGBoost and other tree models also do not naturally extrapolate far beyond the target values represented in their training data.

## Next Experiments

Log-transform the target.

Weighted loss for high-volatility observations.

Quantile regression.

Walk-forward validation.

Tail-specific evaluation.

## Evaluation

MAE  
MSE  
RMSE  
R²

The project also evaluates model behavior specifically during high-volatility periods.

## Repository

Volatility Forecasting.ipynb contains the data collection, feature engineering, model training, evaluation, and plots.

Market data is collected directly in the notebook.

## Status

Baseline models and failure analysis completed.

Next: tail-aware volatility forecasting.
