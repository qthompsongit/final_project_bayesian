### Bayesian Hierarchical Logistic Regression for 2-Year Mortality in Pediatric CNS Tumors

Team: [Team Member Names]
Date: December 2025

### 1. Why Pediatric CNS Tumors Matter?

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

REASON:
The 2-year overall survival (OS) endpoint is a clinically standard benchmark in pediatric CNS oncology. In our cohort, the survival distribution (median ≈ 501 days, mean ≈ 872 days) shows that the 2-year threshold provides a meaningful separation between early mortality and long-term survivors, while avoiding heavy censoring bias.

⸻

Our approach combines:
	•	Horseshoe priors for sparse feature selection
	•	Hierarchical structure to model multi-study heterogeneity

⸻

Feature Engineering
	•	One-hot encoded treatment types
	•	Age at diagnosis with median imputation + missingness indicator
	•	Race & sex expanded into atomic indicator variables
	•	Leakage-free construction using only pre-outcome predictors

⸻

Final Dataset
	•	~800 pediatric CNS tumor patients
	•	Outcome imbalance: ~117 : ~700 (death : survival)
	•	Censored-before-2-year cases fully excluded from classification

### 4. Initial EDA and Bayes Net

After some data engineering, we analyzed the chi squared contingency for our features and our main target to understand what are the most important features to consider for the final model. For further analysis, we also created two bayesian networks: one including our target variable and one without.

### 5. Bayesian Modeling Methodology
Model Type

Bayesian Hierarchical Logistic Regression with Horseshoe Priors

This model combines:

	•	**Logistic regression** for binary survival outcome

	•	**Horseshoe priors** for sparse, data-driven feature shrinkage

	•	**Hierarchical** (multi-level) random intercepts to model differences across studies (Study ID)

This structure allows the model to:

	•	Identify the **most influential** clinical and demographic predictors

	•	Handle high-dimensional features robustly

	•	Borrow statistical strength across multiple studies

	•	Reduce overfitting in small sub-cohorts

**Likelihood**

The model assumes that each patient has a binary outcome indicating whether they died within two years. This outcome is modeled using a logistic likelihood, meaning the model estimates the probability of death as a function of the patient’s clinical and demographic features. The logistic function converts the linear predictor into a probability between 0 and 1, allowing the model to express uncertainty naturally.

For each patient, the predicted log-odds of death is composed of two parts:

A study-specific intercept, which captures baseline differences across studies (for example, some studies may involve patients with systematically higher risk).

A weighted combination of patient-level features, where each feature receives a coefficient that indicates its contribution to increasing or decreasing mortality risk.

The hierarchical intercepts allow each study to have its own baseline while still sharing information across studies. This “partial pooling” prevents small studies from producing unstable estimates and ensures that all studies contribute to a coherent global model. The Horseshoe prior applied to the feature coefficients encourages sparsity, shrinking uninformative predictors toward zero while allowing strong signals to stand out.

Overall, the likelihood expresses the idea that each patient’s observed outcome is generated based on a probability specific to their features and their study membership, and this probability is then linked to the binary outcome through the logistic formulation.


### 6. PyMC Implementation (Core Block)

** with pm.Model() as hier_hs_logistic:

    X_data = pm.Data("X_data", X_train)
    y_data = pm.Data("y_data", y_train)
    study_idx_data = pm.Data("study_idx", study_train)

    tau = pm.HalfStudentT("tau", nu=3, sigma=1.0)
    lam = pm.HalfStudentT("lam", nu=3, sigma=1.0, shape=n_features)
    z = pm.Normal("z", 0, 1, shape=n_features)
    beta = pm.Deterministic("beta", z * lam * tau)

    mu_alpha = pm.Normal("mu_alpha", 0, 5)
    sigma_alpha = pm.HalfNormal("sigma_alpha", 2)
    alpha_raw = pm.Normal("alpha_raw", 0, 1, shape=n_studies)
    alpha = pm.Deterministic("alpha", mu_alpha + alpha_raw * sigma_alpha)

    logits = alpha[study_idx_data] + pm.math.dot(X_data, beta)
    pm.Bernoulli("y_obs", logit_p=logits, observed=y_data)

    nuts = pm.NUTS(target_accept=0.97, max_treedepth=15)
    trace_hier = pm.sample(draws=2000, tune=3000, chains=4, random_seed=42)
**

### 7. Bayesian Network Results

We observe our landmark target benchmark at 11.1% of the recorded cases in our data. The most common combination of demographic features from participants in our model were white, male participants who had received a surgical procedure treatment. Average survival time in days for our participants was 508 days. The average age of diagnosis and last contacted days are relatively similar at 3194 and 3677 respectively. 

For our selection of features (treatment type, treatment agent, race, sex at birth, age group, survival group, and age last contact group), we verified their association with our target variable by evaluating their chi squared contingency. Notably, all of our variables has some significant association with our target variable, with the most pronounced significance outside of our survival grouping variable being the treatment type for a patient.

Our bayesian network models revealed two key important factors that played a key role in the construction of our final model. Both of our models attain BIC scores of -19410.74 and -20139.21, with models trained with the constrained hill climb methodology working the best. In both models, sex at birth was not found to have any significant conditional association with any of our variables. While treatment agent attains a conditional association with our target variable through treatment type when our target is introduced to the bayesian network, it loses that association with the target variable.

Across our bayesian networks, we note a difference in survival times for different races, where shorter survival times attain conditional probabilities for non-white participants less than 0.15, while longer survival times survival times have conditional probabilities for white participants in upwards of 0.5. This may be due to the class imbalance in our data, as the majority of participants studied where white. 

Medium survival times have high conditional probabilites with treatment types like cellular transplants (0.75), while long survival times have high conditional probabilites for long survival times (0.11). Both of these results make sense as these are both prove, common, and effective treatments for various forms of CNS. We also noted some interesting applications of treatments across age ranges as listed below.


Child | Radiation = 0.88

Toddlers | Cellular Therapy = 0.71

Teenagers | Electron Beam Radiation = 0.8

Adolescents | Photon Beat Radiation = 0.3

### 8. Hiearchical Bayesian Logistic Regression Results
Model Performance

The hierarchical Bayesian logistic regression with Horseshoe priors demonstrated strong predictive performance. By allowing study-specific intercepts and partial pooling, the model successfully captured differences across study populations while avoiding overfitting in smaller groups. When evaluated on the held-out test set, the model achieved an AUC of approximately 0.92, indicating excellent discrimination between patients who survived beyond two years and those who did not.

Compared to the non-hierarchical model, which achieved an AUC around 0.87, the hierarchical version provided a meaningful improvement. This suggests that incorporating study-level structure helps the model better generalize and reduces noise arising from heterogeneous data sources.

Posterior Coefficients

The posterior estimates showed that only a subset of predictors had substantial influence on mortality risk. The Horseshoe prior effectively shrank uninformative features toward zero while allowing strong predictors to remain prominent. Treatment-related variables dominated the influential coefficients, with some modalities showing large positive associations with two-year mortality. Demographic and race-related variables showed smaller but interpretable effects.

The hierarchical structure also stabilized these estimates, allowing the model to use information from large studies while still adjusting for differences in smaller cohorts.

Convergence Diagnostics

Sampling diagnostics indicated good overall behavior. Divergences after tuning were minimal (three in total), substantially lower than in earlier non-hierarchical runs. The improvement reflects better geometry in the posterior distribution due to the hierarchical formulation and more appropriate priors. Effective sample sizes were adequate across parameters, and R-hat values were close to 1.0, indicating successful convergence of all chains.

Interpretation

The results imply that hierarchical modeling provides a clearer and more reliable understanding of which factors drive early mortality. The shrinkage effect improves interpretability by removing noise, and the study-level intercepts account for underlying differences in patient populations. Overall, the hierarchical Bayesian model yields more stable estimates and stronger predictive performance than the simpler logistic regression.

### 8. Conclusion
This project demonstrated the value of Bayesian hierarchical modeling for survival prediction in a heterogeneous clinical dataset. By incorporating study-level structure and applying Horseshoe priors for feature shrinkage, the final model achieved both strong predictive accuracy and interpretable parameter estimates. The hierarchical framework allowed the model to borrow strength across studies while still accounting for underlying differences in patient populations, resulting in improved stability and higher AUC compared to the non-hierarchical approach.

The shrinkage behavior of the Horseshoe prior played an important role in clarifying which treatments and demographic variables contributed meaningfully to early mortality risk. At the same time, the model avoided overfitting by suppressing noisy or weakly informative predictors. Together, these elements produced a more robust and clinically interpretable representation of the factors associated with two-year survival.

Overall, the analysis highlights how Bayesian hierarchical methods can provide a principled and effective way to address real-world data challenges such as small subgroup sizes, imbalance across studies, and high-dimensional feature sets. This approach not only yielded better predictive performance but also produced insights that can inform future modeling efforts and guide potential extensions, such as incorporating time-to-event survival models or hierarchical treatment effects.
### 9. Contact

For questions or further exploration, please open an issue in this repository or contact the project team.
