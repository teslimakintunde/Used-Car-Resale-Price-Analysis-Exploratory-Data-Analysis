# Used Car Resale Price Analysis — Exploratory Data Analysis

## 1. Business Context

Used-car pricing is a critical decision for sellers seeking to maximize resale value while remaining competitive in the market. Setting an unrealistic asking price can discourage potential buyers, while underpricing can reduce the seller's potential return.

This project explores historical automobile price records and vehicle specifications to identify the characteristics and market segments associated with price variation. The analysis examines manufacturer, engine configuration, vehicle dimensions, performance, fuel efficiency, and other attributes to understand their relationship with vehicle prices.

The findings provide an analytical foundation for developing a machine learning model that estimates a reference resale price from vehicle characteristics.

---

## 2. Business Problem

Sellers need a reliable starting point for pricing vehicles, but estimating a reasonable resale price can be challenging when vehicle specifications, manufacturer positioning, and performance characteristics vary considerably.

Without a data-driven benchmark, pricing decisions may rely heavily on subjective judgment or inconsistent comparisons.

The business problem is to determine which vehicle characteristics and categorical attributes are most strongly associated with price, and how these relationships can support a consistent, data-driven automobile price estimation model.

---

## 3. Analytical Objectives

- **Identify price drivers**: Evaluate the relationships between vehicle prices and technical specifications, manufacturer, and vehicle configuration.
- **Understand price segmentation**: Compare price distributions across manufacturers, body styles, drivetrain types, fuel systems, and engine configurations.
- **Validate statistical relationships**: Use correlation analysis and appropriate statistical tests to distinguish meaningful patterns from relationships that may be attributable to sampling variation.
- **Assess data quality**: Identify missing values, distributional skewness, unusual observations, and inconsistent or potentially redundant features.
- **Engineer relevant features**: Evaluate derived variables that may capture vehicle size, engine displacement, fuel efficiency, and performance relative to weight.
- **Prepare for predictive modeling**: Identify suitable features, preprocessing requirements, and validation strategies for regression modeling.
- **Translate findings into decisions**: Develop recommendations for vehicle price benchmarking and subsequent model development.

---

## 4. Key Business Questions

| Business Question | Analytical Approach |
|-------------------|---------------------|
| How are automobile prices distributed, and how much variation exists? | Descriptive statistics, distribution plots, and outlier analysis |
| Which numerical vehicle characteristics have the strongest relationships with price? | Pearson and Spearman correlation analysis |
| Do prices differ significantly across manufacturers and vehicle categories? | Group-level price comparisons and Kruskal–Wallis tests |
| Which manufacturers occupy the higher and lower price segments? | Manufacturer-level median and average price analysis |
| How do engine size, horsepower, vehicle weight, and fuel efficiency relate to price? | Correlation analysis and feature comparisons |
| Are some predictors measuring overlapping vehicle characteristics? | Feature correlation and multicollinearity assessment |
| Do engineered features provide additional pricing information? | Derived-feature analysis and comparative model evaluation |
| What data quality issues could affect price estimates? | Missing-value, distribution, category-frequency, and outlier checks |
| Which features should be prioritized for regression modeling? | Combined EDA evidence and out-of-sample model validation |

---

## 5. Data Quality & Preparation

Before interpreting EDA findings or training a predictive model, the dataset must be checked for completeness, consistency, and analytical suitability.

### Data Quality Checks

| Check | Findings / Action |
|-------|-------------------|
| **Dataset completeness** | The supplied summary contains 200 records and 26 columns, including the target variable `price`. |
| **Missing values** | `normalized_losses` has 36 missing values (18%); `bore` and `stroke` each have 4 (2%); `num_of_doors`, `horsepower`, and `peak_rpm` each have 2 (1%). |
| **Target variable** | `price` has 200 non-null observations in the supplied summary. Confirm that prices are numeric, positive, and expressed in a consistent currency. |
| **Data types** | Numerical specifications should be numeric, while attributes such as `make`, `fuel_type`, `body_style`, and `drive_wheels` should be treated as categorical. |
| **Category consistency** | Standardize category labels and whitespace; verify unexpected or inconsistent values before analysis. |
| **Duplicate records** | Check for exact duplicate rows and investigate them before removal. Similar vehicle specifications alone do not establish that a record is a duplicate. |
| **Outliers and skewness** | Inspect unusually high or low prices and skewed numerical features. Validate unusual observations instead of automatically deleting them. |
| **Feature redundancy** | Assess highly correlated predictors, including `city_mpg` and `highway_mpg`, before deciding whether feature selection or regularization is appropriate. |
| **Derived features** | Validate the calculations and definitions of engineered variables, including displacement, volume, and power-to-weight proxies. |
| **Model readiness** | Perform imputation, encoding, and any scaling inside a fitted preprocessing pipeline to avoid information leakage. |

### Data Preparation Decisions

- Convert invalid numeric placeholders into missing values and confirm that all numerical columns have appropriate data types.
- Investigate duplicate records and resolve inconsistent categorical values.
- Retain valid price observations and document any exclusions with a clear rationale.
- Handle missing values using methods appropriate to each feature and fit imputers on training data only.
- Inspect skewness and potential outliers, particularly in `price`, `engine_size`, `horsepower`, and `compression_ratio`.
- Review highly correlated variables and compare alternative feature sets during modeling rather than removing predictors based on correlation alone.
- Separate training and test data before fitting preprocessing steps or selecting model parameters.

> **Data limitation**: The supplied summary establishes that the dataset contains automobile prices and technical specifications, but does not establish the provenance, transaction dates, or whether the prices specifically represent used-car resale transactions. These details should be verified before presenting the model as a validated estimator of current used-car resale values.

# EDA Insights & Business Recommendations

## 6. Executive Summary

The analysis examined **200 automobiles** across **22 manufacturers**, identifying significant price differences across vehicle brands, engine configurations, drivetrain types, and performance characteristics.

Engine size, curb weight, horsepower, and premium-brand classification showed the strongest numerical associations with price. Statistical testing also identified manufacturer, fuel system, cylinder configuration, and drivetrain as important sources of price variation.

These findings informed feature selection, preprocessing, and the design of a regression model for automobile price estimation.

---

## 7. Key Findings

| Business Area | Evidence | Insight |
|---------------|----------|---------|
| **Price Distribution** | Mean: $13,206; median: $10,270; range: $5,118–$45,400 | Prices are right-skewed, with a relatively small number of expensive vehicles lifting the average. |
| **Engine & Performance** | Engine size: *(r = 0.87)*; horsepower: *(r = 0.81)* | Larger engines and higher horsepower are strongly associated with higher prices. |
| **Vehicle Size** | Curb weight: *(r = 0.83)*; width: *(r = 0.75)* | Larger, heavier vehicles tend to occupy higher price points. |
| **Fuel Efficiency** | Highway MPG: *(r = -0.71)*; city MPG: *(r = -0.69)* | More fuel-efficient vehicles tend to have lower prices in this sample, potentially reflecting differences in engine size and vehicle segment. |
| **Brand Positioning** | Premium-brand indicator: *(r = 0.76)* with price | Brand positioning is strongly associated with price and may capture differences not fully explained by technical specifications. |
| **Categorical Differences** | Manufacturer: *(H = 122.12)*; fuel system: *(H = 120.71)*; cylinders: *(H = 76.45)*; drivetrain: *(H = 92.62)* | Price distributions differ significantly across several vehicle categories in the reported Kruskal–Wallis tests. |
| **Price Segmentation** | Budget and mid-market vehicles represent 73.5% of the sample | The dataset is concentrated in lower and middle price segments, with fewer observations available for luxury vehicles. |

> **Note:** The reported Pearson correlations measure linear association, not causation. Statistical significance does not necessarily imply practical or predictive importance.

---

## 8. Statistical Significance of Categorical Features

The Kruskal–Wallis tests indicate that price distributions differ across most tested categorical features.

| Feature | Kruskal–Wallis H | Reported Significance | Effect Size *(η²)* on Log Price |
|---------|------------------|-----------------------|---------------------------------|
| Manufacturer | 122.12 | Significant | 0.72 |
| Fuel system | 120.71 | Significant | 0.52 |
| Number of cylinders | 76.45 | Significant | 0.53 |
| Drive wheels | 92.62 | Significant | 0.47 |
| Engine type | 37.57 | Significant | 0.20 |
| Body style | 25.00 | Significant | 0.15 |
| Engine location | 7.72 | Significant | 0.07 |
| Aspiration | 18.86 | Significant | 0.07 |
| Fuel type | 4.21 | Significant | 0.02 |
| Number of doors | 3.60 | Not significant | 0.01 |

### Business Interpretation

Manufacturer, fuel system, cylinder configuration, and drivetrain appear to distinguish price distributions particularly strongly in this sample. Number of doors did not meet the reported 5% significance threshold.

These results support retaining categorical features for model evaluation rather than relying exclusively on numerical specifications.

> **Statistical caveat:** The reported *p*-values are rounded to two decimal places, so values displayed as 0.00 should be reported as *(p < 0.01)*, not *(p = 0)*. The effect-size calculation and multiple-testing implications should also be verified before interpreting the reported *η²* values as definitive.

---

## 9. Feature Relationships & Multicollinearity

The analysis identified several highly correlated feature pairs:

| Feature Pair | Absolute Correlation |
|--------------|----------------------|
| City MPG – Highway MPG | 0.97 |
| Length – Curb Weight | 0.88 |
| Wheelbase – Length | 0.88 |
| Width – Curb Weight | 0.87 |
| Length – Width | 0.86 |

### Modeling Implication

These features contain overlapping information. This may affect coefficient stability in linear regression, although correlated predictors do not automatically reduce the predictive performance of every model.

### Recommended Approach

- Retain relevant features initially and establish a baseline.
- Evaluate **Ridge Regression** to help manage correlated predictors.
- Compare feature importance and held-out performance before removing variables.
- Avoid selecting features solely because their individual correlations with price are high.

---

## 10. Engine Configuration & Derived Features

The engineered features provide additional perspectives on pricing:

| Feature | Pearson Correlation with Price | Interpretation |
|---------|--------------------------------|----------------|
| Premium-brand indicator | +0.76 | Strong association with price positioning |
| Volume proxy | +0.63 | Vehicle size proxy is associated with higher prices |
| Calculated displacement | +0.53 | Engine capacity-related measure contributes pricing information |
| Power-to-weight ratio | +0.51 | Relative engine output is associated with price |
| Horsepower-per-litre proxy | +0.13 | Weak linear relationship in this sample |
| MPG gap | -0.24 | Relatively weak negative association |

Average MPG also showed a strong negative relationship with price (Pearson: **-0.70**; Spearman: **-0.83**).

> **Recommendation:** Compare the predictive contribution of engineered features against their underlying variables. Derived features may capture useful relationships, but they should not be assumed to improve accuracy without validation.

---

## 11. Data Quality & Modeling Considerations

- **Missing values:** `normalized_losses` has 18% missing data; `bore` and `stroke` each have 2% missing. Apply imputation within the model pipeline.
- **Skewed target:** Price skewness is 1.81. Compare raw-price modeling with a log-transformed target using appropriate inverse transformation and evaluation in the original price units.
- **Small sample size:** With only 200 observations, use cross-validation and control model complexity.
- **Uneven category representation:** Some manufacturers and vehicle configurations have very few observations, which can make category-level estimates unstable.
- **Correlated predictors:** Compare linear and regularized models with tree-based regressors.
- **Historical-price limitations:** The model can estimate prices represented by the training data, but current market estimates require relevant and sufficiently recent price records.

---

## 12. Business Recommendations

1. **Use a multi-factor pricing model**  
   Combine manufacturer, engine size, horsepower, curb weight, drivetrain, fuel system, and relevant body and engine characteristics.

2. **Benchmark within comparable vehicle segments**  
   Compare vehicles with similar brands, specifications, and price positioning instead of relying on a single market-wide average.

3. **Evaluate price-segment performance separately**  
   Measure errors across budget, mid-market, upper-market, and luxury categories to identify where estimates are less dependable.

4. **Control overfitting**  
   Use cross-validation, regularization, and a held-out test set because the dataset is small relative to the number of available features.

5. **Validate engineered features**  
   Test whether premium-brand indicators, displacement estimates, volume proxies, and power-to-weight ratios improve out-of-sample accuracy.

6. **Strengthen real-world used-car pricing**  
   Where available, incorporate vehicle age, mileage, mechanical condition, service history, accident history, location, and transaction date.

---

## . Conclusion

The EDA indicates that automobile pricing reflects a combination of **brand positioning**, **engine capability**, **vehicle size**, **fuel efficiency**, and **configuration**. Numerical correlations, categorical statistical tests, and multicollinearity analysis provide a foundation for informed regression modeling.

The next step is to determine which features deliver reliable predictive value through cross-validation and unseen-data evaluation, then present the model output as a **reference price with clearly communicated limitations**.
