# Baseline Model

**[Notebook](baseline_model.ipynb)**

## Baseline Model Results

### Model Selection
- **Baseline Model Type:** Linear regression (ordinary least squares (OLS))
- **Rationale:** OLS assumes no endogeneity and serves as the benchmark against which IV bias correction is measured. 
It is also computationally efficient, and universally understood, making it the one of the most common baseline model to predict a continuous outcome based on one or more predictor variables. 

### Model Performance
- **Evaluation Metric:** R², adjusted-R², F-statistic, p-value, MSE / RMSE. 
- **Performance Score:** 
    - R²: It is expected to be high for near-identical variable pairs (r=0.99), low for genuine causal chains (0.05-0.25). Neither is "good" or "bad", they refect the data structure. 
    - adjusted-R²: Same interpretation as R², minor downward adjustment for the single predictor. 
    - F-stat: >10 for genuine causal chains. Below 10 signals no real relationship. 
    - p-value: <0.001, except null chain (visits - purchases, expect p > 0.05)
    - RMSE: only meaningful relative to the scale of the outcome variable and compared across models (OLS vs 2SLS on the same chain)

    Note: For near-identical variable paris (r > 0.99), R² is expected = 0.99, very low RMSE, p < 0.001. High fit reflects co-movement, not causal activity. 
    For genuine causal chains (IV datasets), R² is expected to be lower (0.05-0.25), higher RMSE, OLS coefficient biased upward relative to 2SLS, as OLS conflates causal and confounded variation. 
    For null chains, R² is expected to be zero, p > 0.05. OLS correctly finds no relationship here. 
- **Cross-Validation Score:** NA. There is no generalization objective. The goal is unbiased estimation of a causal effect, not minimization of out-of-sample prediction error. 

### Evaluation Methodology
- **Data Split:** NA
- **Evaluation Metrics:** NA

### Metric Practical Relevance
The metrics listed above evaluate statistical fit: 

- R²: Proportion of outcome variance explained 
- adjusted-R²: R² penalized for number of predictors
- F-statistic: Joint significance of the model 
- p-value: significance of the treatment coefficient
- MSE / RMSE: Average prediction error in outcome units 

Note: The evaluation metrics of OLS evaluate statistical fit only. A high R² or low RMSE in OLS does not imply causal validity. If the treatment is endogenous, OLS is biased regardless of how well it fits the data.
The gap between the OLS coefficient and the 2SLS coefficient (quantified in the sensitivity analysis) is the true performance measure of the baseline relative to the causal model.

## Next Steps
This baseline model serves as a reference point for evaluating more sophisticated models in the [Model Definition and Evaluation](../3_Model/README.md) phase.
