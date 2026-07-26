# Dataset Characteristics

**[Notebook](exploratory_data_analysis.ipynb)**

## Dataset Information

### Dataset Source
- **Dataset Link:** https://www.kaggle.com/datasets/cloverchen/causalpitfalls-benchmark-causal-data-neurips-2025
- **Dataset Owner/Contact:** CausalPitfalls: Benchmark Causal Data NeurIPS 2025
CausalPitfalls Benchmark: Structured Synthetic Datasets for Evaluating LLM Causa

### Dataset Characteristics
- **Number of Observations:** 1,000 samples per dataset (5,000 total)
- **Number of Features:** 4 variables per dataset (instrument, treatment, mediator/confounder where applicable, outcome). 5 datasets in total. Each dataset contains ≥500 samples generated from a well‐specified structural causal model (SCM) so that ground‐truth effects are known. Continuous numerical variables throughout; no categorical features, no missing values. 

### Target Variable/Label
- **Label Name:** Causal direction and effect size (varies by dataset)
- **Label Type:** Causal identification. The "label" is the ground-truth causal
  structure encoded in the SCM, against which estimated directions and coefficients are evaluated.
- **Label Description:** For each dataset, the goal is to identify which variable causally affects which, and to estimate the magnitude of that effect. The ground truth is the structural equation defining the causal relationship in the SCM.
- **Label Values:** Binary causal direction (X_1 → X_2 or X_2 → X_1 for the benchmark dataset; domain-specific directions for the others) and a continuous effect size β representing the unit causal effect of the treatment on the outcome.
- **Label Distribution:** Not applicable in the traditional sense. Each dataset has one true causal direction and one true causal coefficient β. The task is identification, not prediction across a label distribution.

### Feature Description
**causal_direction_iv_sem (Benchmark IV dataset)**
- **Z_1, Z_2 (instruments):** Two independent uniform random variables serving as instrumental variables. Continuous, range [-1, 1]. Uniform histogram confirms exogeneity — no relationship with outcome except through treatment X_1.
- **X_1 (treatment):** Endogenous treatment variable caused by Z_1 and Z_2. Continuous, approximately normal, range [-4, 3]. The true causal direction runs X_1 → X_2.
- **X_2 (outcome):** Outcome caused by X_1. Continuous, approximately normal, range [-6, 6].

**clinical_trial_sem**
- **baseline_health (proposed IV):** Patient baseline health score prior to treatment. Continuous, range [0, 1], uniform distribution. Fails the relevance assumption (first-stage F = 0.86) — not a valid IV for dosage.
- **dosage_mg (treatment):** Drug dosage administered in milligrams. Continuous, range [10, 50], approximately uniform.
- **drug_concentration (mediator):** Resulting drug concentration in blood. Continuous, range [15, 75]. Near-perfectly correlated with dosage_mg (r = 0.998) — statistically inseparable from dosage.
- **health_improvement (outcome):** Measured improvement in patient health score. Continuous, range [10, 55], right-skewed distribution.

**ecommerce_sem**
- **age (instrument, Chain 1):** Customer age in years. Continuous, range [18, 65], approximately uniform. Strong instrument for visits (F = 124,406, r = 0.996) but no direct effect on purchases (exclusion holds). 
- **income (treatment, Chain 2):** Annual customer income in dollars. Continuous, range [30,000, 120,000], approximately uniform. Direct causal driver of purchases (r = 0.999). Two independent causal chains — age/visits and income/purchases — are uncorrelated with each other (r ≈ 0.006).
- **visits (mediator, Chain 1):** Number of site visits. Continuous, range [10, 52]. Near-perfectly correlated with age (r = 0.996) but has no causal effect on purchases (2SLS t = 1.72, not significant).
- **purchases (outcome):** Total purchase value in dollars. Continuous, range [400, 1250]. Caused by income (β = 0.01 per dollar, R² = 0.998), not by visits.

**environment_sem**
- **rainfall_mm (instrument):** Annual rainfall in millimetres. Continuous, range [0, 200], approximately uniform. Valid instrument for soil quality (r = 0.999, F = 859,535). Satisfies exclusion restriction — affects crop yield only through soil quality.
- **fertilizer_kg (direct treatment):** Fertilizer applied in kilograms. Continuous, range [50, 200], approximately uniform. Direct causal driver of crop yield (β = 1.24, R² = 0.845). Independent of rainfall (r = 0.081).
- **soil_quality (treatment/mediator):** Soil quality index score. Continuous, range [0, 100], approximately uniform. Near-perfectly correlated with rainfall (r = 0.999). Causal effect on crop yield: β = 0.95 (2SLS).
- **crop_yield (outcome):** Crop yield in units per hectare. Continuous, range [100, 320], approximately normal. Driven by both fertilizer (β = 1.20) and soil quality (β = 0.80) in an additive combined model.

**marketing_sem**
- **ad_spend_k (degenerate IV):** Advertising spend in thousands of dollars. Continuous, range [0, 100], approximately uniform. Near-perfectly correlated with brand_awareness (r = 0.999) — same construct, degenerate IV.
- **promo_spend_k (direct treatment):** Promotional spend in thousands of dollars. Continuous, range [0, 50], approximately uniform. Direct causal driver of sales (β = 1.29, R² = 0.604). Independent of ad_spend (r = -0.014).
- **brand_awareness (treatment/degenerate):** Brand awareness index score. Continuous, range [0, 90]. Statistically identical to ad_spend_k (r = 0.999). Using ad_spend as IV for brand_awareness is degenerate — instrumenting a variable with itself.
- **sales (outcome):** Sales volume in units. Continuous, range [0, 120], approximately normal. Driven by both promo_spend (β = 1.29) and ad_spend/brand_awareness (β ≈ 0.53, treat with caution).
## Approaches to implement 
 
- **Method 1: Two-Stage Least Squares (2SLS)** — primary causal estimator for chains with valid instruments. Isolates exogenous variation in the treatment using the instrument to produce unbiased causal effect estimates. Applied to: causal_direction_iv and  environment (soil chain).

- **Method 2: Direction Test Based on IV Strength** — compares R² of instruments with each candidate treatment and outcome variable. The variable the instrument explains better is the treatment; the other is the outcome. Confirmed causal direction X_1 → X_2 in causal_direction_iv (R² ratio 0.253 vs 0.183).

- **Method 3: Control Function Approach (CF)** — equivalent to 2SLS in linear models but adds the Hausman endogeneity test as a by-product. The significance of the first-stage residual in the second stage confirms whether IV correction was necessary. Applied to: causal_direction_iv and environment (soil chain).

- **Method 4: OLS Baseline** — naive estimator assuming no endogeneity. Applied to all chains as the baseline for bias quantification. For chains with no valid IV (ecommerce income, environment fertilizer, marketing promo), OLS is the primary estimator, validated by Oster (2019) δ sensitivity.

- **Method 5: Anderson-Rubin CI** — weak-instrument-robust confidence interval. Applied conditionally to clinical_trial (F = 0.86) where standard 2SLS CIs are unreliable. Remains valid regardless of instrument strength.

## Exploratory Data Analysis

The exploratory data analysis is conducted in the [exploratory_data_analysis.ipynb](exploratory_data_analysis.ipynb) notebook, which includes:

- Data loading and initial inspection (shape, dtypes, head)
- Statistical summaries and distributions (mean, std, min, max per variable)
- Missing value analysis (confirmed: zero missing values across all datasets)
- Pairplot visualisation to identify linear relationships, instrument candidates (uniform histograms), and near-perfect correlations
- Correlation matrices to detect degenerate variable pairs (r ≥ 0.99) and confirm causal chain independence
- Std/range ratio heuristic to flag uniform distributions as IV candidates
- Variable distribution assessment confirming all relationships are linear (justifying 2SLS over nonlinear IV methods)
