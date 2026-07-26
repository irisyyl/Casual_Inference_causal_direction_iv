# Baseline Model

**[Notebook](baseline_model.ipynb)**

## Baseline Model Results

### Model Selection
- **Baseline Model Type:** Linear regression (ordinary least squares, OLS)
- **Rationale:** OLS assumes no endogeneity and treats the treatment variable as if it were randomly assigned. It serves two roles in this project: 

    (1) the naive benchmark against which IV bias correction is measured — the gap between OLS and 2SLS coefficients quantifies the cost of ignoring endogeneity; and 

    (2) the primary estimator for chains where no valid instrument exists (ecommerce income, environment fertilizer, marketing promo), validated by Oster (2019) sensitivity analysis. 
  
  OLS is computationally efficient, universally understood, and produces interpretable coefficients, making it the natural starting point before introducing IV methods.


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

- **Cross-Validation Score:** Not applicable. There is no generalisation objective. The goal is unbiased estimation of a causal effect, not minimisation of out-of-sample prediction error. Train/test splits and cross-validation are ML concepts that do not apply to causal identification.

### Evaluation Methodology
- **Data Split:** Not applicable. Causal inference uses all available data to maximise the precision of the causal effect estimate.
- **Evaluation Metrics:** Each OLS chain is evaluated on two levels: 
  (1) statistical fit metrics (R², F-stat, p-value, RMSE) to characterise the relationship strength; and 
  
  (2) causal validity, assessed by comparing OLS coefficients against 2SLS estimates (endogenous chains) or Oster δ sensitivity (OLS-only chains). Statistical fit alone does not establish causal validity.

### Metric Practical Relevance

The metrics listed above evaluate statistical fit only: 

| Metric | What it measures | Causal relevance |
|---|---|---|
| R² | Proportion of outcome variance explained by treatment | Low in 2SLS by design — not a quality indicator |
| Adjusted-R² | R² penalised for number of predictors | Same interpretation as R² |
| F-statistic | Joint significance of the OLS model | Distinct from first-stage F in 2SLS — does not measure IV strength |
| p-value | Significance of the treatment coefficient | Meaningful for testing H0: β=0, but not for causal validity |
| MSE / RMSE | Average prediction error in outcome units | Only meaningful relative to outcome scale; not a causal metric |


**Critical note:** A high R² or low RMSE in OLS does not imply causal validity. If the treatment is endogenous (correlated with the error term), OLS is biased regardless of how well it fits the data. The clinical trial dosage chain demonstrates this — R² = 0.984 is extremely high, yet the estimate is untrustworthy because no valid instrument exists to test or correct for endogeneity.

The most informative OLS "performance score" in this project is not R² or RMSE but the **endogeneity bias** — the gap between the OLS coefficient and the 2SLS coefficient, which directly quantifies how much the naive estimate is distorted by unmeasured confounding.

## Next Steps
This baseline model serves as a reference point for evaluating more sophisticated models in the [Model Definition and Evaluation](../3_Model/README.md) phase.

The specific questions carried forward are:

1. Where OLS is biased (causal_direction_iv, environment soil), does 2SLS successfully recover the true causal effect, and by how much does it differ from OLS?
2. Where OLS appears reliable (ecommerce income, environment fertilizer, marketing promo), does Oster sensitivity confirm robustness, and does the absence of a valid IV justify reporting OLS as the primary estimate?
3. Where OLS fails entirely (clinical trial — no valid IV), what is the appropriate inference tool, and what does the failure reveal about the limits of observational causal identification?