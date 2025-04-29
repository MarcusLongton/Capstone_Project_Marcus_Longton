# Capstone Project: Exploratory Data Analysis (EDA) and Baseline Model

## Overview

This project is the initial stage of my Capstone, focusing on exploratory data analysis (EDA), feature engineering, and the development of a baseline machine learning model.  
The goal of this phase is to understand the underlying structure and relationships within the dataset and build a simple baseline model to predict turbidity, which will be further refined in Module 24.

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
  - **Train MSE**: ~6075
  - **Test MSE**: ~6993
  - **Train R²**: ~0.3589
  - **Test R²**: ~0.4914

These results indicate that the model was able to capture meaningful variance in turbidity based on the available environmental features. The model performed better than the dummy baseline, especially on unseen test data, suggesting some generalization capability.

Turbidity appears to correlate most strongly with wave height, wave period, and water temperature. Outliers and nonlinear relationships may be influencing performance.

## Next Steps

In Module 24, I will enhance this analysis with the following:

- **Model Exploration**:  
  - Train additional models such as `RandomForestRegressor`, `GradientBoostingRegressor`, and `XGBoost`.
  - Compare their performance with Linear Regression to evaluate improvements in handling nonlinear relationships and feature interactions.

- **Feature Engineering**:  
  - Add **polynomial features** (e.g., squared wave height, interaction terms) to help linear models capture curvature and interactions.
  - Experiment with **lagged features** (e.g., turbidity shifted by 1–3 time steps) to capture temporal influence.
  - Consider reducing variance with **log-transformation** of highly skewed targets.

- **Hyperparameter Tuning**:  
  - Use `GridSearchCV` or `RandomizedSearchCV` to optimize model parameters (e.g., number of trees, max depth).
  - Evaluate using cross-validated metrics like R², RMSE, and MAE.

- **Model Evaluation and Communication**:  
  - Plot residuals, feature importances, and partial dependence plots.
  - Prepare final documentation with both **technical** and **non-technical** summaries.


## Directory Structure

```plaintext
Capstone_EDA.ipynb    # Jupyter notebook containing all code, visualizations, and modeling
README.md             # Project summary and results
