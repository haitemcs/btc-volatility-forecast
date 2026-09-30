# BTC Volatility Forecasting

> *The object is not to make the market obedient to the model, but to discover the structure that remains when the noise is removed.*

This project studies a simple question: **how much of the next hour's volatility is determined by the information already present in the market?**

The work is deliberately experimental. Models are treated as mathematical objects to be compared, broken, and understood—not merely as sources of a single score.

## Mathematical Formulation

Let the hourly OHLCV observation at time $t$ be

$
X_t = (O_t,H_t,L_t,C_t,V_t).
$

The hourly range is defined by

$
R_t = 100\frac{H_t-L_t}{L_t}.
$

The close-to-close return is

$
r_t = 100\frac{C_t-C_{t-1}}{C_{t-1}}.
$

For a window of $k$ hours, a volatility estimate is

$
\sigma_t^{(k)}
=
\sqrt{\frac{1}{k-1}
\sum_{i=0}^{k-1}(r_{t-i}-\bar r_t)^2}.
$

The forecasting problem is then written as

$
y_{t+1}=f(X_t,X_{t-1},\ldots,X_{t-p}),
$

where $y_{t+1}$ is the future volatility ratio and $f$ is the learned function.

For Ridge regression,

$
\hat\beta
=
\arg\min_\beta
\left[
\sum_{i=1}^{n}(y_i-x_i^T\beta)^2
+
\lambda\|\beta\|_2^2
\right].
$

The neural network learns a nonlinear map

$
\hat y=f_\theta(x),
$

by minimizing the empirical loss

$
\mathcal L(\theta)
=
\frac{1}{n}\sum_{i=1}^{n}
(y_i-f_\theta(x_i))^2.
$

This last equation is important: under ordinary MSE, the model is driven toward the conditional mean. When violent volatility is rare, the geometry of the objective itself can favor the ordinary regime over the exceptional one.

The project therefore asks not only **which model predicts better**, but **what mathematical structure causes the failures**.

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

- `Volatility Forecasting.ipynb` — complete data collection, feature construction, model training, evaluation, and visual analysis.
- No static `btc_data.csv` is required; the notebook retrieves the market data directly from Binance.

## Experimental Philosophy

The progression of the project is intentionally close to a mathematical investigation:

1. Define the quantity.
2. Construct the observable variables.
3. Build simple models.
4. Measure their errors.
5. Examine where the errors concentrate.
6. Explain the failure from the model's structure.
7. Modify the objective.
8. Test whether the modification changes the phenomenon.

A numerical result is only the beginning. The more interesting object is the **reason for the result**.

In that sense, the project is less concerned with declaring a champion than with finding the invariants of the problem: which behaviors survive a change of model, which disappear, and which arise from the assumptions imposed by the learner itself.

## Status


**Current stage:** Baseline model evaluation and failure analysis.

**Next stage:** Tail-aware modeling with target transformation, weighted loss, and quantile regression.
