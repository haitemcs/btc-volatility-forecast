# BTC Volatility Forecasting

Machine learning experiments for forecasting the **Bitcoin hourly volatility ratio**, with a focus on model evaluation, tail-risk behavior, and out-of-sample performance.

## Model Evaluation & Results

### Overview

Three model architectures were evaluated for predicting the BTC hourly volatility ratio:

- Neural Network
- Ridge Regression
- XGBoost

The comparison focuses not only on aggregate metrics, but also on how each model behaves across normal and high-volatility market regimes.

### Findings

#### Normal Market Conditions (0.5–1.5 ratio)

All three models capture routine hourly volatility reasonably well. Predictions cluster relatively closely around the ideal prediction line in this range.

#### High-Volatility Spikes (>2.0 ratio)

All three models struggle to reproduce extreme volatility spikes.

Actual volatility ratios can reach approximately **3.0–4.0**, while predictions tend to remain in the **1.5–2.0** region. This indicates systematic underprediction of rare tail events.

### Model-Specific Behavior

#### Neural Network

The Neural Network shows greater dispersion in high-volatility regions and attempts to follow larger target values more than the linear baseline, but this comes with higher prediction variance.

#### Ridge Regression

Ridge Regression is comparatively conservative, producing predictions concentrated around the central range. Its regularization contributes to a smoother response and limited sensitivity to extreme target values.

#### XGBoost

XGBoost shows a noticeable upper prediction boundary around the high-volatility region.

This is related to the behavior of tree-based models: standard decision-tree ensembles partition the feature space and assign predictions based on training observations in terminal leaves. They therefore generally do not extrapolate beyond the target behavior represented in the training data.

## Root Cause Analysis

### 1. MSE Loss Bias

Standard Mean Squared Error optimizes the conditional mean. When extreme volatility events are rare, reducing errors on the much larger number of ordinary observations can dominate the objective.

As a result, the model can achieve a reasonable overall loss while still systematically underestimating tail events.

### 2. Data Imbalance Across Volatility Regimes

Extreme volatility surges occur much less frequently than quiet or normal market hours.

Consequently, the training objective is dominated by normal-regime observations, giving the model less incentive to accurately reproduce rare spikes.

### 3. Limited Tree Extrapolation

XGBoost and other standard tree-based regressors do not naturally extrapolate beyond the target ranges represented by their learned terminal regions.

This helps explain the visible prediction ceiling during extreme volatility events.

## Risk Implications

These baseline models can describe normal volatility regimes, but their tendency to underestimate extreme events is important when considering applications such as:

- Risk estimation
- Position sizing
- Volatility-aware trading systems
- Portfolio exposure management

A model that systematically underestimates tail volatility could underestimate risk during market stress.

> **Important:** This project is an ML research/engineering experiment and is not financial advice or a trading recommendation.

## Planned v2 Experiments

### 1. Log-Transform the Target

Apply a logarithmic transformation to the volatility target to reduce skewness and make extreme values easier for the model to learn.

Conceptually:

'log_target = log(1 + volatility_ratio)'

Predictions will then be transformed back to the original scale for evaluation.

### 2. Weighted Loss

Increase the contribution of rare high-volatility observations during training.

For example, experiment with:

- 5× weight for high-volatility observations
- 10× weight for high-volatility observations

The threshold for defining a high-volatility event will be evaluated rather than assumed to be optimal.

### 3. Quantile Regression

Evaluate upper conditional quantiles instead of predicting only the conditional mean.

Candidate quantiles:

- 80th percentile
- 90th percentile

This is intended to investigate whether upper-tail forecasts provide more useful information for risk-sensitive applications.

## Evaluation Strategy

Future experiments will compare models using both overall metrics and regime-specific metrics.

Planned metrics include:

- MAE
- MSE
- RMSE
- R²
- Tail-event MAE
- Tail-event RMSE
- Prediction bias during high-volatility periods

The project will also continue moving toward time-aware validation and walk-forward evaluation to better reflect real forecasting conditions.

## Current Research Direction

The current progression is:

1. Establish baseline models
2. Compare Neural Network, Ridge, and XGBoost
3. Diagnose prediction failures
4. Investigate tail-risk underprediction
5. Apply target transformation and weighted objectives
6. Evaluate quantile forecasting
7. Compare models using walk-forward validation
8. Investigate whether the resulting forecasts are useful for volatility-aware decision systems

## Repository Structure

- 'mytesting.ipynb' — model experiments, preprocessing, training, evaluation, and visualizations
- 'btc_data.csv' — BTC market data used in the experiments

## Status

**Current stage:** Baseline model evaluation and failure analysis.

**Next stage:** Tail-aware modeling with target transformation, weighted loss, and quantile regression.
