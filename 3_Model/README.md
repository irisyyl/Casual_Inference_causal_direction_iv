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
     

- **Actual Performance Results:**

| Chain | β (OLS) | p-value | R² | Adj-R² | F-stat | MSE | RMSE |
|---|---|---|---|---|---|---|---|
| causal_direction_iv (X_1→X_2) | 0.4921 | <0.001 | 0.0802 | 0.0793 | 87.12 | 2.143 | 1.464 |
| clinical_trial (dosage→health) | 0.9967 | <0.001 | 0.9841 | 0.9841 | 62,358 | 0.812 | 0.901 |
| clinical_trial (drug_conc→health) | 0.6645 | <0.001 | 0.9923 | 0.9922 | 127,580 | 0.401 | 0.633 |
| ecommerce (visits→purchases) | 0.5201 | <0.001 | 0.0029 | 0.0019 | 2.92 | 32,847 | 181.2 |
| ecommerce (income→purchases) | 0.0100 | <0.001 | 0.9976 | 0.9976 | 416,767 | 80.1 | 8.95 |
| environment (soil→crop_yield) | 0.9293 | <0.001 | 0.2177 | 0.2169 | 278.3 | 2,418 | 49.17 |
| environment (fertilizer→crop_yield) | 1.2420 | <0.001 | 0.8447 | 0.8445 | 5,430 | 480.8 | 21.93 |
| marketing (brand_awareness→sales) | 0.5099 | <0.001 | 0.3801 | 0.3795 | 613.0 | 374.6 | 19.35 |
| marketing (promo_spend→sales) | 1.2853 | <0.001 | 0.6040 | 0.6036 | 1,522 | 238.6 | 15.45 |

- **Performance Interpretation:**

  **Near-identical variable pairs (r ≥ 0.99) — high R², misleading fit:**
  - clinical_trial dosage/drug_conc chains: R² = 0.984 / 0.992. These reflect co-movement between near-identical variables (r = 0.998), not causal validity. OLS is biased — no valid IV exists to correct it. 
  - ecommerce income→purchases: R² = 0.998. Income is the direct causal driver (r = 0.999). OLS is reliable here — Oster δ confirms robustness.

  **Genuine causal chains (endogenous treatment) — low R², biased β:**
  - causal_direction_iv: R² = 0.080. OLS β = 0.492 vs 2SLS β = 0.932 — OLS underestimates the true causal effect because it uses only the raw (endogenous) correlation, not the instrument-purified variation.
  - environment soil chain: R² = 0.218. OLS β = 0.929 vs 2SLS β = 0.948 — OLS is biased upward due to shared farm management confounders.

  **Null causal chain — near-zero R², correct null result:**
  - ecommerce visits→purchases: R² = 0.003, F = 2.92, p > 0.05. OLS correctly finds no relationship. The 2SLS (t = 1.72) confirms visits have no causal effect on purchases.

  **Direct OLS chains (no IV needed) — moderate to high R²:**
  - environment fertilizer: R² = 0.845. Reliable — Oster δ > 1. 
  - marketing promo_spend: R² = 0.604. Reliable — Oster δ > 1.
  - marketing brand_awareness: R² = 0.380. Treat with caution — degenerate IV (ad_spend r = 0.999 with brand_awareness).



- **Comparative Analysis**: 
    - OLS β vs 2SLS β - bias magnitude and direction 
   **Endogeneity bias:** — the gap between the OLS coefficient and the 2SLS coefficient, which directly quantifies how much the naive estimate is distorted by unmeasured confounding:

| Chain | OLS β | 2SLS β | Bias (OLS−2SLS) | OLS reliable? |
|---|---|---|---|---|
| causal_direction_iv | 0.492 | 0.932 | −0.440 | No — endogenous |
| environment (soil) | 0.929 | 0.948 | −0.019 | No — endogenous |
| ecommerce (income) | 0.010 | N/A | ~0 | Yes — Oster robust |
| environment (fert) | 1.242 | N/A | ~0 | Yes — Oster robust |
| marketing (promo) | 1.285 | N/A | ~0 | Yes — Oster robust |