# Capstone Project: Predicting Ocean Water Visibility (Turbidity) for Spear Fishing.

## Overview

This project is centered around being able to predict when is a good day to go spearfishing. To answer solve this problem, I utilized every tool at my disposal from beginning to end. First with an Exploratory Data Analysis (EDA) to finally training the most advanced models at the end of my examination. 
The goal was to understand the underlying structure and relationships within the dataset and build a model to predict turbidity

## Research Question

**How do environmental variables such as wave height, wave period, water depth, water temperature, and pressure affect turbidity levels in different coastal locations?**

## Dataset

The data for this project comes from the Virginia Coast Reserve Long-Term Ecological Research (VCR LTER) site:  
🔗 [VCR LTER Dataset - knb-lter-vcr.265.3](https://portal.edirepository.org/nis/mapbrowse?packageid=knb-lter-vcr.265.3)

Two monitoring sites were used:
- **Bay** site
- **Flat** site

Each site provided measurements for:
- Water Depth
- Significant Wave Height
- Average Wave Period
- Water Temperature
- Water Pressure
- Turbidity (target variable)

Time data was also available but used only for visualization purposes, not modeling.

## Description of the Data

The raw dataset was sourced from the Virginia Coast Reserve Long-Term Ecological Research (VCR LTER) site.  
Data were collected from two primary locations:
- **Bay Site** — located near a protected coastal bay.
- **Flat Site** — located near a more exposed intertidal flat.

Each location recorded similar environmental measurements. However, slight differences in instrument placement, depth, or environmental exposure could introduce biases when combining data directly.  
To manage this, a `Location` feature was engineered to distinguish between observations from the Bay and Flat sites during modeling.

Additionally, some sensors produced multiple versions of the same measurement at different timestamps (e.g., turbidity sensors at different depths or instrument IDs). Only the most complete and consistent time series from each site were used to build the primary dataset.

**Key considerations:**
- **Time data** were kept for visualization but excluded from model training to prevent temporal leakage.
- **Turbidity spikes** in the data suggest short-term storm events or major tidal shifts.
- **Wave Energy** was engineered to better represent physical forces acting on sediment transport, which affects turbidity.

## Description of Variables

From the raw files, the following main variables were available:

| Raw Variable Name | New Name | Description |
|:---|:---|:---|
| `Bay_Depth_RBR_51114` | `Depth` | Depth at the Bay site (meters) |
| `Bay_Hs_RBR_51114` | `Hs` | Significant wave height at the Bay site (meters) |
| `Bay_Tav_RBR_51114` | `Tav` | Average wave period at the Bay site (seconds) |
| `Bay_WT_RBR_51114` | `WT` | Water temperature at the Bay site (°C) |
| `Bay_Press_RBR_51114` | `Press` | Water pressure at the Bay site (decibar) |
| `Bay_Turb_RBR_054076` | `Turbidity` | Turbidity at the Bay site (NTU) |
| `Flat_Depth_051115` | `Depth` | Depth at the Flat site (meters) |
| `Flat_Hs_051115` | `Hs` | Significant wave height at the Flat site (meters) |
| `Flat_Tav_051115` | `Tav` | Average wave period at the Flat site (seconds) |
| `Flat_WT_051115` | `WT` | Water temperature at the Flat site (°C) |
| `Flat_Pres_051115` | `Press` | Water pressure at the Flat site (decibar) |
| `Flat_Turb_054077` | `Turbidity` | Turbidity at the Flat site (NTU) |

Additionally, the following features were **engineered**:

| Engineered Feature | Description |
|:---|:---|
| `Wave_Energy` | Calculated as Hs² × Tav to better approximate the physical energy delivered by waves |
| `Month`, `DayOfYear`, `Hour` | Extracted from timestamps to allow for seasonality analysis |
| `Location` | Categorical feature indicating 'Bay' or 'Flat' observation |

## Data Selection

For initial modeling:
- Only the following features were used: `Depth`, `Hs`, `Tav`, `WT`, `Press`, `Location`.
- Time was retained only for EDA visualizations (e.g., plotting turbidity over time).
- Turbidity values were predicted as a continuous variable (regression task).

In later specialized analyses (e.g., Bay-only modeling), feature sets were narrowed to:
- `Hs`, `Tav`, `Wave_Energy`, and `WT`

This selection process allowed for a clearer understanding of which physical forces have the greatest influence on turbidity at the Bay site without confounding location-based differences.

---

## Data Cleaning and Feature Engineering

- **Missing Values**: Checked for missing values; none were found that impacted modeling.
- **Duplicate Records**: No duplicate rows detected.
- **Outliers**: Visualized and noted high variability; some extreme turbidity events exist, as expected for coastal systems.
- **Feature Engineering**:
  - Extracted **month**, **day of year**, and **hour** from timestamps.
  - Created a **wave energy** feature using squared wave height multiplied by wave period.
  - Added a **Location** feature to distinguish Bay vs Flat observations.
- **Scaling**: Numerical features were standardized (mean 0, variance 1).
- **Categorical Encoding**: 'Location' was one-hot encoded.

## Exploratory Data Analysis (EDA)

- **Scatterplots** were created to explore the relationship between turbidity and other variables.
- **Rolling averages** were used to detect seasonal trends.
- **Heatmaps** were created to show correlations between variables.
- **Turbidity trends** over time were visualized separately for Bay and Flat sites.
- **Actual vs. Predicted plots** were generated to visually assess model performance.

## Baseline Model

- **Model**: Dummy Regressor (strategy = mean)
- **Goal**: Establish a baseline performance that future models should improve upon.
- **Evaluation Metric**: Mean Squared Error (MSE)
  - **Train MSE**: ~8463
  - **Test MSE**: ~9311
- **Interpretation**: The dummy model provides a baseline error level; any real model should aim to lower this MSE.

## First Real Model: Linear Regression

- **Model**: Standard Linear Regression
- **Preprocessing Pipeline**:
  - StandardScaler for numeric features
  - OneHotEncoder for categorical features
- **Results**:
  - Train MSE: ~ 6075
  - Test MSE: ~ 6993
  - Train R²: ~ 0.3589
  - Test R²: ~ 0.4914

The Linear Regression model achieved a lower MSE than the dummy model, indicating that environmental features do carry predictive power for turbidity.

## Evaluation of Primary Results

Based on initial model performance:

- The **Dummy Regressor** established a baseline with a test MSE of ~9311.
- A **Linear Regression model** using all features achieved:
  - **Test MSE**: ~6993
  - **Test RMSE**: ~83.62

These results indicate that the model was able to capture meaningful variance in turbidity based on the available environmental features. The model performed better than the dummy baseline, especially on unseen test data, suggesting some generalization capability.

Turbidity appears to correlate most strongly with wave height, wave period, and water temperature. Outliers and nonlinear relationships may be influencing performance.

## Utilizing an Auto Machine Learning Framework

To get an idea of what types of more advanced models might perform well, H2O (Auto ML Framework) was utilized to train and tune many different models at the same time. 

- Over many runs with {{max_runtime_sec}} set to between 100 - 500 seconds, **Stacked Ensemble models** consistently had the best scores of between 67 to 65 RMSE depending on the run and runtime.
- Something to note is that on every run, the **XGBOOST** model was only a few tenths of a unit behind the **Stacked Ensemble models** atop the leaderboard.
- From here, I decided that the next best course of action was to train and finetune a **XGBOOST** model by hand. 
- A **Stacked Ensemble Model** using all features achieved:
  - **Test MSE**: ~4391.96
  - **Test RMSE**: ~65.35
  - These scores represent a decrease in error of 27%


## Training an XGBOOST model

From my initial Training without all default hyper-parameters 

- The **XGBOOST** model achived
  - **Test MSE**: ~3692.93
  - **Test RMSE**: ~60.76 
  - This again resulted in a decrease in error of 7% from the previous best model
 
## Fine-Tuning XGBOOST model with GridSearchCV

In order to improve model performance a **GridSearchCV** model selector was utilized.

- The following Parameters composed the {{param_grid}}
  - {{max_depth}} [2,4,6,8]
  - {{n_estimators}} [50,100]
  - {{verbose}} = 1
  - {{n_jobs}} = 1
  - {{cv}} = 5
 
- The best parameters from this model were able to achieve the lowest error score
- The **Tuned XGBOOST** model achived
  - **Test MSE**: ~3561.52
  - **Test RMSE**: ~59.67
  - This resulted in a decrease in error of 1.8% from the previous best model
 
## TabPFN Regressor (Final Model Trained)

TabPFN is a foundational model for tabular data

- The **TabPFN** model achived
  - **Test MSE**: ~4129.6
  - **Test RMSE**: ~64.26
  - This resulted in an increase in error from our **Tuned XGBOOST** model
 
## Conclusion:

This project was able to successfully build a model to predict water visibility (turbidity) with an error of 8.04% (Calculated by dividing the best RMSE over the range of Turbidity in the data).
Additionally, this project was able to identify that the following variables,

- Significant Wave Height {{Hs}}
- Average Wave Period {{Tav}}
- Depth (m) {{Depth}}

Were the best predictors of our target variable Turbidity. 

## Model Performance Summary

| Model                         |     MSE |   RMSE |
|:------------------------------|--------:|-------:|
| XGBoost (GridSearchCV tuned)  | 3561.52 |  59.67 |
| XGBoost (default params)      | 3692.93 |  60.76 |
| TabPFN                        | 4129.6  |  64.26 |
| Stacked Ensemble (H2O AutoML) | 4391.96 |  65.35 |
| Linear Regression             | 6993    |  83.62 |
| Dummy Regressor               | 9311    |  96.49 |










