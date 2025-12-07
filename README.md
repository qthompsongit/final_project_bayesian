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

### 3. Initial EDA and Bayes Net

After some data engineering, we analyzed the chi squared contingency for our features and our main target to understand what are the most important features to consider for the final model. For further analysis, we also created two bayesian networks: one including our target variable and one without.

### 5. Bayesian Modeling Methodology
Model Type

Bayesian Hierarchical Logistic Regression with Horseshoe Priors

Likelihood

### 6. PyMC Implementation (Core Block)

** NEED TO ADD
**

### 7. Results

We observe our landmark target benchmark at 11.1% of the recorded cases in our data. The most common combination of demographic features from participants in our model were white, male participants who had received a surgical procedure treatment. Average survival time in days for our participants was 508 days. The average age of diagnosis and last contacted days are relatively similar at 3194 and 3677 respectively. 

Our bayesian network models revealed two key important factors that played a key role in the construction of our final model. Both of our models attain BIC scores of -19410.74 and -20139.21, with models trained with the constrained hill climb methodology working the best. In both models, sex at birth was not found to have any significant conditional association with any of our variables. While treatment agent attains a conditional association with our target variable through treatment type when our target is introduced to the bayesian network, it loses that association with the target variable.

Across our bayesian networks, we note a difference in survival times for different races, where shorter survival times attain conditional probabilities for non-white participants less than 0.15, while longer survival times survival times have conditional probabilities for white participants in upwards of 0.5. This may be due to the class imbalance in our data, as the majority of participants studied where white. 

Medium survival times have high conditional probabilites with treatment types like cellular transplants (0.75), while long survival times have high conditional probabilites for long survival times (0.11). Both of these results make sense as these are both prove, common, and effective treatments for various forms of CNS. We also noted some interesting applications of treatments across age ranges as listed below.


Child | Radiation = 0.88

Toddlers | Cellular Therapy = 0.71

Teenagers | Electron Beam Radiation = 0.8

Adolescents | Photon Beat Radiation = 0.3


### 8. Conclusion

### 9. Contact

For questions or further exploration, please open an issue in this repository or contact the project team.
