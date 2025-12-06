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

Why 2 years?: 2-year OS is a commonly used endpoint in pediatric CNS oncology, and given our sample’s survival distribution (median ≈ 501 days, mean ≈ 872 days), it provides a clinically meaningful separation between early mortality and longer-term survivors.

Feature Engineering

One-hot encoding of treatment types and sex
Multi-hot atomic encoding of race categories
Age at first diagnosis computed and median-imputed with a missingness indicator
Survival time constructed from age at diagnosis → age at last contact
Removal of rows with missing survival time prior to endpoint creation
Standardization of continuous predictors
Dropping identifiers (Participant ID, Study ID) prior to modeling


Final Dataset

~800 (need to update) pediatric CNS tumor cases/patients
Outcome imbalance ~117:700 (death:survival) (need to update)


### 4. Bayesian Modeling Methodology
Model Type

Bayesian Hierarchical Logistic Regression with Horseshoe Priors

Likelihood

### 5. PyMC Implementation (Core Block)

** NEED TO ADD
**

### 6. Results


### 7. Conclusion

### 8. Contact

For questions or further exploration, please open an issue in this repository or contact the project team.
