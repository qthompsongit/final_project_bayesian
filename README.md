### 📘 Bayesian Hierarchical Logistic Regression for 2-Year Mortality in Pediatric CNS Tumors

Team: [Team Member Names]
Date: December 2025

### 🧠 Why Pediatric CNS Tumors Matter

Pediatric Central Nervous System (CNS) tumors represent a critical area of oncological research due to their high prevalence and mortality rates in children. 

According to a recent review by **Damodharan & Puccetti (2024)**, the clinical urgency is underscored by three key facts:

* **Most Common Solid Neoplasm**
    > *"CNS tumors are the most common solid neoplasm seen in children."*
* **Leading Cause of Cancer-Related Death**
    > *"They are the leading cause of death from cancer in this group."*
* **High Incidence (~20%)**
    > *"CNS tumors account for approximately 20% of childhood cancers, second only to leukemia in overall frequency."*

#### 📉 The Challenge: Data Scarcity & Research Gaps
Despite their clinical importance, pediatric CNS tumors remain under-researched compared to adult cancers. 
* **Data Scarcity:** As a rare disease, sample sizes for pediatric tumors are inherently small and fragmented across different institutions.
* **Limited Analysis:** The lack of large-scale, centralized datasets has historically hindered the application of advanced machine learning models, leaving many risk factors and treatment outcomes underexplored.

**Why This Project Matters:**
This scarcity of data necessitates robust statistical approaches. Our **Bayesian Hierarchical Logistic Model** is specifically designed to handle these limitations—extracting meaningful signals from limited data while quantifying the high uncertainty inherent in pediatric oncology.

🎯 2. Project Overview

This project develops a Bayesian Hierarchical Logistic Regression model to estimate 2-year mortality risk in pediatric CNS tumor patients.

We initially explored CNS vs non-CNS tumor classification but lacked a proper clinical control group. We pivoted to a landmark-style survival endpoint:

1 → death within 730 days

0 → survival ≥ 730 days

censored before 2 years → excluded

Our approach combines:

Horseshoe Priors for sparse feature selection,

Hierarchical structure for multi-study differences,

Posterior uncertainty estimation essential for clinical applications.

📁 3. Data

We use harmonized clinical datasets from the
Childhood Cancer Data Initiative (CCDI) Clinical Data Commons.

Tables Used

Participants

Diagnosis

Treatment

Survival

Note: The Treatment Response table was not included in this model.

Feature Engineering

One-hot encoded treatments

Standardized age at diagnosis

Race & sex expanded into indicator variables

Study ID used as grouping variable in the hierarchical model

Final Dataset

~800 pediatric CNS tumor cases

Outcome imbalance ~1:6 (death:survival)

Four independent studies represented

🧩 4. Bayesian Modeling Methodology
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

    # Horseshoe Prior
    tau = pm.HalfCauchy("tau", beta=1)
    lam = pm.HalfCauchy("lam", beta=1, shape=n_features)
    betas = pm.Normal("betas", mu=0, sigma=tau * lam, dims="coeffs")

    # Hierarchical Intercepts (Non-centered)
    mu_alpha = pm.Normal("mu_alpha", 0, 2)
    sigma_alpha = pm.HalfNormal("sigma_alpha", 2)
    z_study = pm.Normal("z_study", 0, 1, dims="studies")
    alpha_study = pm.Deterministic("alpha_study", mu_alpha + sigma_alpha * z_study)

    # Logistic Model
    logits = alpha_study[study_idx] + pm.math.dot(X_scaled, betas)
    obs = pm.Bernoulli("obs", logit_p=logits, observed=y)

    # Sampling
    idata = pm.sample(2000, tune=2000, target_accept=0.99)

📊 6. Results
1. Feature Effects

Most treatment effects are centered near zero → no strong association

Pharmacotherapy shows a positive association with early mortality
(likely due to confounding by indication)

Age at diagnosis shows a mild protective effect (older → lower 2-year mortality)

2. Study-Level Differences

Hierarchical intercepts reveal large baseline variations:

phs002517 shows a substantially lower baseline mortality

other studies cluster together with higher baseline risk levels

validates the need for hierarchical modeling

3. Model Diagnostics

Using:

standardized predictors

non-centered intercepts

target_accept=0.99

significantly reduced divergences and improved sampling stability.

🚀 7. Conclusion

This project demonstrates how Bayesian Hierarchical Logistic Regression can:

capture multi-study heterogeneity,

identify meaningful predictors using Horseshoe priors,

quantify uncertainty for clinical interpretation,

support the development of robust pediatric oncology risk models.

The methodology is extendable to other outcomes such as progression-free survival, recurrence, or treatment-specific response modeling.

📬 8. Contact

For questions or further exploration, please open an issue in this repository or contact the project team.
