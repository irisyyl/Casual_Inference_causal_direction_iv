# Model Definition and Evaluation

**[Notebook](model_definition_evaluation)**

- **Model Selection**: OLS (baseline) and two-stage least squares (2SLS) with control function (CF) as the causal estimator. Non-linear ML-based IV methods (e.g. DeepIV) are not applicable, given that all datasets show clear linear relationships with 4–5 variables, making additional model complexity unnecessary.

- **Feature Engineering**: Feature engineering is not necessary in this project. This project aims to identify causal effects. Feature engineering can introduce bias and confound causal pathways. Additionally, feature engineering reduces complexity and dimensions of the data. Concerning the datasets we used have simple structures (4-5 variables), the causal relationships are already fully expressed in the raw variables and thus no feature engineering is needed. 

- **Hyperparameter Tuning**: No hyperparameter tuning is needed as this project aims to estimate causal effects, not minimizing prediction error. 

- **Implementation**: 
  The analysis follows an 8-step pipeline applied across 5 datasets:
  1. *Pairplot exploration* — visualise variable distributions and pairwise relationships to identify instrument candidates.
  2. *Correlation diagnostics* — confirm causal chains, detect near-perfect correlations (r ≥ 0.99) indicating degenerate IVs or same-construct variables
  3. *First-stage F-stat check* — validate instrument relevance (threshold: F > 10, Staiger-Stock rule).
    - If F < 10, it is flagged as weak instrument. 
  4. *2SLS with corrected SEs* — estimate causal effect using manual two-stage OLS with corrected residuals for valid inference.
    - If weak instrument is detected in step 3, run Anderson-Rubin CI alongside 2SLS
  5. *Direction test + placebo* — confirm causal arrow direction and test cross-chain exclusion. 
  6. *OLS comparison* — quantify endogeneity bias as (OLS β − 2SLS β). 
  7. *Sensitivity analysis* — Imbens (2003) partial to assess robustness to unmeasured confounding on datasets with valid IVs or Oster sensitivity on OLS-only chains 
  8. *Control Function (CF)* — formal Durbin-Wu-Hausman (Hausman test) endogeneity test via first-stage residuals

- **Evaluation Metrics**: 
    Instrument validity: 
    - First-stage F-statistic > 10 (Staiger-Stock)
    - Exclusion R² ratio > 3 (exclusion restriction check)
    - Hausman test via CF residuals
    - Anderson-Rubin (AR) test (applied conditionally when first-stage F < 10 (weak instrument). Generates confidence intervals that remain valid regardless of instrument strength, unlike standard 2SLS CIs which break down under weak instruments.)
    
    Exclusion restriction validation: 
    - Placebo tests 
    - Overidentification / Sargan-Hansen (when applicable)
    - Subsample / cross-chain tests
    
    Estimate robustness:                  
    - Imbens (2003) partial-R² sensitivity — quantifies the minimum partial correlation an unmeasured confounder would need with both treatment and outcome simultaneously to reduce the IV estimate to zero. Reported as robustness value (RV = √breakdown_product).
    - Oster (2019) δ sensitivity - applied to OLS-only chains (clinical_trial fallback, ecommerce income, environment fertilizer, marketing promo). Reports δ: how much unobservables must matter relative to observables to explain away the OLS estimate. δ > 1 indicates robustness.
     
- **Comparative Analysis**: 
    - OLS β vs 2SLS β - bias magnitude and direction 