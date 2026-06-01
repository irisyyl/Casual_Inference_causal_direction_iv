# Dataset Characteristics

**[Notebook](exploratory_data_analysis.ipynb)**

## Dataset Information

### Dataset Source
- **Dataset Link:** https://www.kaggle.com/datasets/cloverchen/causalpitfalls-benchmark-causal-data-neurips-2025
- **Dataset Owner/Contact:** CausalPitfalls: Benchmark Causal Data NeurIPS 2025
CausalPitfalls Benchmark: Structured Synthetic Datasets for Evaluating LLM Causa

### Dataset Characteristics
- **Number of Observations:** [Total number of samples/records in your dataset. For time series data, also specify the temporal resolution (e.g., daily, hourly, etc.)]
- **Number of Features:** 5 datasets in total, each dataset has 4 features. Each dataset contains ≥500 samples generated from a well‐specified structural causal model (SCM) so that ground‐truth effects are known.

### Target Variable/Label
- **Label Name:** [Name of the target variable/column]
- **Label Type:** [Classification/Regression/Clustering/Other]
- **Label Description:** [What does this label represent? What is the prediction task?]
- **Label Values:** [For classification: list of classes and their meanings. For regression: range of values. For other tasks: describe the label structure]
- **Label Distribution:** [Brief description of class balance for classification or value distribution for regression]

### Feature Description
[Provide a brief description of each feature or group of features in your dataset. If you have many features, group them logically and describe each group. Include information about data types, ranges, and what each feature represents.]

**Example format:**
- **Feature 1 (feature_name):** [Description of what this feature represents, data type, and any relevant details]
- **Feature 2 (feature_name):** [Description of what this feature represents, data type, and any relevant details]
- **Feature Group (group_name):** [Description of a group of related features]

## Approaches to implement 

- **Method 1:** Two-Stage Least Squares (2SLS)
- **Method 2:** Direction Test Based on IV Strength
- **Method 3:** Control Function Approach

## Exploratory Data Analysis

The exploratory data analysis is conducted in the [exploratory_data_analysis.ipynb](exploratory_data_analysis.ipynb) notebook, which includes:

- Data loading and initial inspection
- Statistical summaries and distributions
- Missing value analysis
- Feature correlation analysis
- Data visualization and insights
- Data quality assessment
