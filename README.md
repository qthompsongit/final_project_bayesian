### Bayesian Hierarchical Logistic Regression for 2-Year Mortality in Pediatric CNS Tumors

Team: [Team Member Names]
Date: December 2025

  ### Why Pediatric CNS Tumors Matter?

Pediatric Central Nervous System (CNS) tumors represent a critical area of oncological research due to their high prevalence and mortality rates in children. This project is motivated by the urgent clinical need to better understand the prognostic factors driving survival in this vulnerable population.

#### 1. Clinical Urgency
According to a recent review by **Damodharan & Puccetti (2024)**, the significance of pediatric CNS tumors is underscored by three key facts:

* **Most Common Solid Neoplasm:**
    > *"CNS tumors are the most common solid neoplasm seen in children."*
* **Leading Cause of Cancer-Related Death:**
    > *"They are the leading cause of death from cancer in this group, surpassing even leukemia in mortality impact."*
* **High Incidence (~20%):**
    > *"CNS tumors account for approximately 20% of childhood cancers, second only to leukemia in overall frequency."*

#### 2. The Challenge: Data Scarcity & Heterogeneity
Despite their clinical importance, robust statistical analysis of pediatric CNS tumors has been historically challenging due to the nature of pediatric oncology data:

* **Data Scarcity:** As a rare disease, sample sizes for pediatric tumors are inherently small and fragmented across different institutions.
* **Limited Analysis:** The lack of large-scale, centralized datasets has historically hindered the application of advanced machine learning models, leaving many risk factors and treatment outcomes underexplored.

**Why This Project Matters:**
This scarcity of data necessitates robust statistical approaches. Our **Bayesian Hierarchical Logistic Model** is specifically designed to handle these limitations—extracting meaningful signals from limited data while quantifying the high uncertainty inherent in pediatric oncology.

### 2. Data

Our analysis will use real-world, open-access clinical data from the Childhood Cancer Data Initiative (CCDI) Clinical Data Commons, specifically the Childhood Cancer Data Catalog (C3DC). 

We utilize **four harmonized structured tables: Participants, Diagnosis, Survival,
and Treatment**, which provide comprehensive phenotype information, including sex, race, anatomical tumor site, treatment characteristics, and survival status with follow-up time. 

CNS tumor labels are derived directly from standardized anatomical site codes
(e.g., C70–C72 and related CNS-specific regions). 

All data are publicly accessible through the
CCDI Explore Portal: https://clinicalcommons.ccdi.cancer.gov/explore

### 3. Project Overview

This project develops a Bayesian Hierarchical Logistic Regression model to estimate 2-year mortality risk in pediatric CNS tumor patients.

We build a landmark-style survival endpoint:

1 → death within 730 days
0 → survival ≥ 730 days
censored before 2 years → excluded

**REASON: NEED TO ADD
**

Our approach combines:

Horseshoe Priors for sparse feature selection,
Hierarchical structure for multi-study differences

Feature Engineering

One-hot encoded treatments
Standardized age at diagnosis
Race & sex expanded into indicator variables

Study ID used as grouping variable in the hierarchical model

Final Dataset

~800 pediatric CNS tumor cases

Outcome imbalance ~1:6 (death:survival)

Four independent studies represented

### 4. Bayesian Modeling Methodology
Model Type

Bayesian Hierarchical Logistic Regression with Horseshoe Priors

Likelihood
y_i ~ Bernoulli(p_i)
logit(p_i) = α_study[i] + X_i β

Hierarchical Random Intercepts

Non-centered parameterization is used to stabilize sampling:

z_j ~ Normal(0, 1)
α_j = μ_α + σ_α * z_j

Horseshoe Prior for Sparse Feature Selection
β_k ~ Normal(0, τ * λ_k)
τ  ~ HalfCauchy(1)
λ_k ~ HalfCauchy(1)


This shrinks irrelevant predictors toward zero and highlights clinically meaningful ones.

🛠️ 5. PyMC Implementation (Core Block)
with pm.Model(coords=coords) as hierarchical_model:

** NEED TO ADD
**

### 6. Results
1. Feature Effects

2. Study-Level Differences


3. Model Diagnostics

Using:



### 7. Conclusion

This project demonstrates how Bayesian Hierarchical Logistic Regression can:

capture multi-study heterogeneity,

identify meaningful predictors using Horseshoe priors,

quantify uncertainty for clinical interpretation,

support the development of robust pediatric oncology risk models.

The methodology is extendable to other outcomes such as progression-free survival, recurrence, or treatment-specific response modeling.

📬 8. Contact

For questions or further exploration, please open an issue in this repository or contact the project team.
