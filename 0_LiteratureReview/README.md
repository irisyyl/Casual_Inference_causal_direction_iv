# Literature Review

Approaches or solutions that have been tried before on similar projects.

**Summary of Each Work**:

- **Source 1**: Causality: Models, Reasoning, and Inference by Judea Pearl 

  - **[Link](https://archive.illc.uva.nl/cil/uploaded_files/inlineitem/Pearl_2009_Causality.pdf)**
  - **Objective**: To provide a rigorous mathematical framework for causal reasoning, distinguishing causation from correlation and formalising the conditions under which causal effects can be identified from observational data.
  - **Methods**: Directed Acyclic Graphs (DAGs) to represent causal structures; do-calculus to define interventions (do(X=x)) separately from observations (X=x); structural causal models (SCMs) to encode functional relationships between variables; backdoor and frontdoor criteria to identify confounding paths.
  - **Outcomes**: A complete calculus for causal identification that determines when a causal effect is estimable from observational data and what adjustment is required. The do-calculus was shown to be complete — any identifiable causal quantity can be derived from it.
  - **Relation to the Project**: Provides the theoretical foundation for the entire project. The DAG framework is used to represent the causal structure of each dataset (instrument → treatment → outcome), to define what the exclusion restriction means structurally (no direct path from IV to outcome), and to justify why IV estimation recovers a causal effect rather than a mere association.

- **Source 2**: Instrumental Variables in Causal Inference and Machine Learning: A Survey

  - **[Link](https://arxiv.org/abs/2212.05778)**
  - **Objective**: To systematically survey IV methods across both classical causal inference and modern machine learning, covering identification assumptions, estimation strategies, and diagnostic tools for practitioners.
  - **Methods**: Two-Stage Least Squares (2SLS) for linear IV estimation; Control Function Approach (CFA) as an equivalent alternative that yields an endogeneity test; generalised method of moments (GMM) for overidentified systems; deep IV and kernel IV for nonlinear settings; evaluation criteria including first-stage F-statistic, exclusion restriction tests, and Sargan-Hansen overidentification tests.
  - **Outcomes**: A unified taxonomy of IV estimators showing when each is appropriate. The survey demonstrates that 2SLS and CFA are equivalent in linear models but diverge under nonlinearity, and that ML-based IV methods require high-dimensional data or nonlinear treatment-outcome relationships to outperform classical 2SLS.
  - **Relation to the Project**: Directly informs the model selection decision. The survey's finding that ML-based IV methods are not beneficial for low-dimensional linear settings justified the choice of 2SLS and CFA over DeepIV or kernel IV. It has informed the evaluation criteria (F-statistic, exclusion ratio, Hausman test) used throughout the project.

- **Source 3**: Causal Inference: The Mixtape by Scott Cunningham

  - **[Link](https://mixtape.scunning.com/)**
  - **Objective**: To provide an accessible, applied introduction to causal inference methods for social scientists and practitioners, covering the design-based approach to identification.
  - **Methods**: Potential outcomes framework (Rubin causal model); Directed Acyclic Graphs (DAGs) for identification; randomised controlled trials as the benchmark; instrumental variables including 2SLS and the Local Average Treatment Effect (LATE) interpretation; regression discontinuity, difference-in-differences, and matching as alternative identification strategies.
  - **Outcomes**: Practical guidance on selecting and validating identification strategies, with emphasis on the conditions under which each method recovers a credible causal estimate. The LATE interpretation clarifies that IV estimates the causal effect for compliers — units whose treatment status is changed by the instrument — rather than the average treatment effect for the full population.
  - **Relation to the Project**: Provided the applied framework for understanding DAGs and IV assumptions used in this project. Cunningham's treatment of the exclusion restriction — that the instrument must affect the outcome only through the treatment — is the basis for the exclusion R² ratio check and placebo tests.

- **Source 4**: Causal Inference: What If by Miguel A. Hernán and James M. Robins

  - **[Link](https://static1.squarespace.com/static/675db8b0dd37046447128f5f/t/677676888e31cc50c2c33877/1735816881944/hernanrobins_WhatIf_2jan25.pdf)**
  - **Objective**: To develop a rigorous, unified framework for causal inference from observational data using counterfactual reasoning, with applications in epidemiology, medicine, and public health. 
  - **Methods**: Potential outcomes and counterfactual definitions of causal effects; identifiability conditions (exchangeability, positivity, consistency); inverse probability weighting (IPW); standardisation and marginal structural models; instrumental variables as a special case of causal identification under unmeasured confounding; g-estimation for structural nested models.
  - **Outcomes**: A comprehensive treatment of the assumptions required for causal identification and the consequences of assumption violations. The book demonstrates that no method — including IV — recovers causal effects without untestable assumptions, and that sensitivity analysis is therefore an essential complement to any causal estimate.
  - **Relation to the Project**:Sensitivity analysis. Hernán and Robins' emphasis on the fundamental untestability of the exclusion restriction motivates the use of placebo tests, subsample checks, and the Imbens (2003) partial-R² framework to assess robustness. Causal identification from observational data is not always possible and that study design (randomisation) is often the only solution.

- **Source 5**: Instrumental variables regression with weak instruments by Douglas Staiger and James H. Stock (May 1997)

  - **[Link](https://stock.scholars.harvard.edu/sites/g/files/omnuum5911/files/stock/files/instrumental_variables_regression_with_weak_instruments.pdf)**
  - **Objective**: To characterise the finite-sample behaviour of IV estimators when instruments are weakly correlated with the endogenous regressor, and to provide practical diagnostic tools for detecting weak instruments in applied research.
  - **Methods**: Asymptotic theory under weak instrument sequences (instruments with first-stage correlation shrinking at rate 1/√n); concentration parameter as the theoretical measure of instrument strength; first-stage F-statistic as the practical diagnostic; comparison of 2SLS, LIML, and Fuller estimator performance under weak instruments; simulation evidence on bias and size distortion. 
  - **Outcomes**: The influential rule of thumb that a first-stage F-statistic below 10 indicates weak instruments, under which 2SLS confidence intervals are unreliable — they undercover the true parameter and the estimator has substantial bias toward OLS. LIML was shown to be more robust than 2SLS under weak instruments but still unreliable when F is very small. The paper motivated the subsequent development of weak-instrument-robust inference methods including the Anderson-Rubin test.
  - **Relation to the Project**: Evaluation metric of IV strength. 





