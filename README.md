# Used Car Resale Price Analysis — Exploratory Data Analysis

## 1. Business Context

Used-car pricing is a critical decision for sellers who want to maximise resale value while staying competitive. An unrealistic asking price can discourage buyers, while underpricing reduces the seller's return.

This project explores 200 historical automobile sales and their specifications to identify the characteristics and market segments associated with price variation. It examines manufacturer, engine configuration, dimensions, performance, fuel efficiency and other attributes, and their relationship with price.

The findings are the analytical foundation for a machine learning model that estimates a reference resale price from vehicle characteristics.

## 2. Business Problem

Sellers need a reliable starting point for pricing, but estimating a reasonable resale price is hard when specifications, manufacturer positioning and performance vary considerably. Without a data-driven benchmark, pricing relies on subjective judgement or inconsistent comparisons.

The business problem is to determine which vehicle characteristics and categorical attributes are most strongly associated with price, and how those relationships can support a consistent, data-driven price estimation model.

## 3. Analytical Objectives

- Identify price drivers: relate price to technical specifications, manufacturer and vehicle configuration.
- Understand price segmentation: compare price distributions across manufacturers, body styles, drivetrains, fuel systems and engine configurations.
- Validate statistical relationships: use correlation analysis and statistical tests, with multiple-testing correction, to separate real patterns from sampling variation.
- Assess data quality: identify missing values, skewness, unusual observations and redundant features.
- Engineer relevant features: evaluate derived variables for size, displacement, fuel efficiency and power relative to weight.
- Prepare for predictive modelling: identify suitable features, preprocessing requirements and a validation strategy.
- Translate findings into decisions: recommend a pricing-benchmark approach and next steps.

## 4. Key Business Questions

| Business Question | Analytical Approach | Answer |
|-------------------|---------------------|--------|
| How are prices distributed, and how much variation exists? | Descriptive statistics, distribution plots, IQR outlier analysis | Right-skewed: mean 13,206 vs median 10,270, skew 1.81 (0.68 after log) |
| Which numeric characteristics relate most strongly to price? | Pearson and Spearman correlation | Engine size 0.87, curb weight 0.83, horsepower 0.81, width 0.75 |
| Do prices differ significantly across manufacturers and categories? | Group comparisons, Kruskal–Wallis with Holm correction, η² | Yes for all categories except fuel type and door count after correction; make is strongest (η² 0.72) |
| Which manufacturers occupy the higher and lower price segments? | Median and mean price by make, with sample-size check | Jaguar, Porsche, Mercedes-Benz, BMW highest; Honda, Chevrolet, Plymouth, Dodge lowest |
| How do engine size, horsepower, weight and fuel efficiency relate to price? | Correlation analysis, feature comparison | Positive for size, power and weight; negative for MPG (a car-class effect) |
| Do predictors overlap? | Correlation matrix, VIF | Yes: city/highway MPG r = 0.97; VIF above 10 for both MPG columns and curb weight |
| Do engineered features add pricing information? | Correlation of derived features with price and log price | Average MPG and volume proxy are strongest; whether they add predictive value is tested only in modelling |
| What data quality issues could affect estimates? | Missing-value, distribution, category-frequency, outlier checks | Non-random missing normalized_losses; rare categories; skewed price |
| Which features should be prioritised for modelling? | Combined EDA evidence | Make, engine size, curb weight, horsepower, drive wheels, body style, cylinders |

## 5. Data Quality & Preparation

Assumptions: prices are treated as historical sold prices of used cars, in the dataset's own units. The file contains no sale date, currency, model year, mileage or condition, so findings describe specification and brand effects, not condition-adjusted values.

### Data Quality Checks

| Check | Result |
|-------|--------|
| Completeness | 200 records × 26 columns; the target price has no missing values |
| Missing values | normalized_losses 36 (18%); bore, stroke 4 each (2%); num_of_doors, horsepower, peak_rpm 2 each (1%) |
| Data types | Numeric specifications are numeric; ten descriptors (make, fuel type, body style, drive wheels and others) treated as categorical |
| Category consistency | One label error, peugot, corrected to peugeot (22 makes after correction) |
| Duplicates | 0 exact duplicate rows |
| Logical validation | 0 violations: no non-positive prices, impossible dimensions or implausible MPG ordering |
| Outliers and skewness | 14 price outliers (IQR), all genuine premium cars, kept; compression_ratio outliers (13.5%) come from diesel engines |
| Feature redundancy | City/highway MPG r = 0.97; VIF 27.6 and 25.2; curb weight VIF 16.4 |
| Derived features | Calculated and checked: displacement, volume proxy, power-to-weight, average MPG, MPG gap |

### Data Preparation Decisions

- Missing values imputed on a copy: normalized_losses by median within symboling group, door count by mode within body style, other gaps by median within make. A missing-value flag is kept for normalized_losses.
- All valid prices retained; no rows excluded.
- For modelling, imputation, encoding and scaling must be fitted inside training folds to avoid leakage.

Non-random missingness: cars missing normalized_losses have a median price of 16,500 versus 9,268 for the rest (Mann-Whitney p ≈ 1.5e-08). The gap is concentrated in luxury makes (every Alfa Romeo, Isuzu, Mercury and Renault; most Porsche, Jaguar and BMW cars), so imputation alone would hide a signal.

Assumptions and limitations: the dataset has no provenance, transaction date or currency information, so the model should be presented as a specification-based benchmark rather than a validated estimator of current resale values.

---

# EDA Insights & Business Recommendations

## 6. Executive Summary

The analysis examined 200 automobiles across 22 manufacturers and found significant price differences across brands, engine configurations, drivetrain types and performance characteristics.

Engine size, curb weight and horsepower show the strongest numerical associations with price. Statistical testing identifies manufacturer, fuel system, cylinder configuration and drivetrain as the largest categorical sources of price variation. Brand also carries information beyond specifications: Porsche, BMW and Audi are priced well above what their engine size, weight and power predict.

These findings informed feature selection, preprocessing and the design of a regression model for price estimation.

## 7. Key Findings

| Business Area | Evidence | Insight |
|---------------|----------|---------|
| Price Distribution | Mean 13,206; median 10,270; range 5,118–45,400; skew 1.81 | Right-skewed: a small number of expensive cars lift the average. A log transform reduces skew to 0.68 |
| Engine & Performance | Engine size r = 0.87; horsepower r = 0.81 | Larger, more powerful engines are strongly associated with higher prices |
| Vehicle Size | Curb weight r = 0.83; width 0.75; length 0.69 | Larger, heavier cars occupy higher price points |
| Fuel Efficiency | Highway MPG r = −0.71; city MPG −0.69 | More economical cars tend to be cheaper in this sample, reflecting engine size and car class rather than a causal effect |
| Brand Positioning | Make η² = 0.72 (adjusted 0.69). After controlling for engine size, weight and horsepower: Porsche ≈ +52%, BMW ≈ +44%, Audi ≈ +29%, Jaguar ≈ −25% | Brand carries pricing information beyond specifications. Jaguar's gap rests on 3 cars with very large engines |
| Configuration | Rear-wheel drive median 16,913 vs front-wheel 8,192; six-cylinder 21,038 vs four-cylinder 8,949; turbo 15,686 vs standard 9,418 | Drivetrain, cylinders and aspiration separate price tiers |
| Categorical Differences | Kruskal–Wallis H: make 122.12, fuel system 120.71, drive wheels 92.62, cylinders 76.45 | Price distributions differ significantly across these categories |
| Price Segmentation | Budget (<8K) and Mid (8–16K) cars are 73.5% of the sample; Luxury (30K+) is 7% (14 cars) | The data is concentrated in lower and middle segments; luxury estimates rest on few cars |
| Specification Power | Engine size, weight and horsepower alone explain about 85% of log-price variance (in-sample) | Most, but not all, of price is physical; age, mileage and condition are not in the data |
| Missing Data | normalized_losses 18% missing, concentrated in luxury makes | Missingness is informative, not random |

Pearson correlations measure linear association, not causation, and statistical significance does not imply practical importance.

## 8. Statistical Significance of Categorical Features

Kruskal–Wallis tests compare price distributions across categories. Groups containing a single car are excluded from each test. p-values are corrected across the ten tests with the Holm method.

| Feature | Groups tested | Kruskal–Wallis H | p-value | Significant after Holm correction | η² (log price) | Adjusted η² |
|---------|---------------|------------------|---------|-----------------------------------|----------------|-------------|
| Manufacturer | 21 | 122.12 | < 0.001 | Yes | 0.72 | 0.69 |
| Fuel system | 6 | 120.71 | < 0.001 | Yes | 0.52 | 0.50 |
| Drive wheels | 3 | 92.62 | < 0.001 | Yes | 0.47 | 0.47 |
| Number of cylinders | 5 | 76.45 | < 0.001 | Yes | 0.53 | 0.52 |
| Engine type | 6 | 37.57 | < 0.001 | Yes | 0.20 | 0.18 |
| Body style | 5 | 25.00 | < 0.001 | Yes | 0.15 | 0.13 |
| Aspiration | 2 | 18.86 | < 0.001 | Yes | 0.07 | 0.06 |
| Engine location | 2 | 7.72 | 0.005 | Yes | 0.07 | 0.07 |
| Fuel type | 2 | 4.21 | 0.040 | No (adjusted p = 0.080) | 0.02 | 0.01 |
| Number of doors | 2 | 3.60 | 0.058 | No | 0.01 | 0.01 |

### Business Interpretation

- Manufacturer, fuel system, cylinder configuration and drivetrain separate price distributions most strongly. Body style and engine type are moderate; aspiration and engine location are small effects.
- Fuel type is nominally significant (p = 0.040) but not after multiple-testing correction, and its effect is negligible. Door count is not significant.
- These results support keeping categorical features in the model rather than relying on numeric specifications alone.

Caveats: η² is computed on log price and rises mechanically with the number of categories, which is why the adjusted value is shown: manufacturer has 22 levels for 200 cars, so part of its 0.72 is mechanical. Engine location rests on only 3 rear-engine cars. Several categories overlap (fuel system and engine type partly reflect engine size and car class), so these effects are not independent.

## 9. Feature Relationships & Multicollinearity

| Feature Pair | Absolute Correlation |
|--------------|----------------------|
| City MPG – Highway MPG | 0.97 |
| Length – Curb Weight | 0.88 |
| Wheelbase – Length | 0.88 |
| Width – Curb Weight | 0.87 |
| Length – Width | 0.86 |
| Curb Weight – Engine Size | 0.85 |

Variance inflation factors confirm the redundancy: city MPG 27.6, highway MPG 25.2, curb weight 16.4; length 9.8, horsepower 8.9 and wheelbase 8.7 are close behind.

### Modelling Implication

These features carry overlapping information. This can destabilise coefficients in plain linear regression, although correlated predictors do not automatically hurt every model's predictive performance.

### Recommended Approach

- Replace the two MPG columns with one average-MPG feature.
- Compare Ridge or other regularised regression with tree-based models.
- Compare feature importance and held-out performance before removing further variables.
- Do not select features solely because their individual correlation with price is high.

## 10. Engine Configuration & Derived Features

Engine configuration separates price tiers: eight-, five- and six-cylinder cars have median prices of about 38,008, 21,398 and 21,038, versus 8,949 for four-cylinder cars (156 of the 200). By engine type, ohcv (19,699) and dohc (16,500) sit far above the common ohc (9,095). By fuel system, mpfi cars have a median of 15,985 versus 7,324 for 2bbl. Diesels have a higher median than gas cars (13,853 vs 9,989), but only because 13 of the 20 diesels are turbocharged; diesel-standard cars have a median of 7,898.

| Derived Feature | Correlation with Price | With Log Price | Interpretation |
|-----------------|------------------------|----------------|----------------|
| Premium-brand indicator | +0.76 | +0.73 | Target-derived, see below |
| Volume proxy (length × width × height) | +0.63 | +0.70 | Size proxy associated with higher prices |
| Calculated displacement (bore² × stroke) | +0.53 | +0.60 | Engine-capacity measure, moderately associated |
| Power-to-weight ratio | +0.51 | +0.52 | Relative output moderately associated |
| Horsepower-per-litre proxy | +0.13 | +0.23 | Weak relationship |
| MPG gap (highway − city) | −0.24 | −0.22 | Weak negative association |
| Average MPG | −0.70 | −0.78 | Strong negative; Spearman −0.83 |

The premium-brand indicator must not be used as a model input. It is built from each make's median price, so its correlation with price is partly circular and would leak the target into any model. Use make itself, or build brand groupings inside each cross-validation fold.

Recommendation: compare the predictive contribution of engineered features against their underlying variables. Average MPG is the clearest candidate because it removes the MPG redundancy; the others should not be assumed to improve accuracy without validation.

## 11. Data Quality & Modelling Considerations

- Missing values: normalized_losses 18%, bore and stroke 2% each. Impute inside the model pipeline. The normalized_losses gap is informative, but the field is an insurer-assigned rating that a seller would not know.
- Skewed target: price skew is 1.81. Model log(price) and evaluate in original units and in percentage terms; compare against a raw-price baseline.
- Small sample: with 200 cars and only 14 in the luxury tier, use repeated cross-validation rather than a single split, and control model complexity.
- Uneven category representation: seven makes have fewer than 5 cars; rear-engine (3), rotor (4), three- and twelve-cylinder (1 each) cars are rare. Group rare levels and treat their estimates as fragile.
- Correlated predictors: compare regularised linear models with tree-based regressors.
- Seller-facing features: a model for sellers should use inputs a seller knows (spec-sheet and registration details). Exclude symboling and normalized_losses (weak signal, |r| ≤ 0.2, and unknown to sellers) and rarely known engine internals (bore, stroke, compression_ratio, peak_rpm, fuel_system).
- Historical-price limitations: the model can only estimate prices represented in its training data; current estimates need recent price records.

## 12. Business Recommendations

- Use a multi-factor pricing model. Combine manufacturer, engine size, horsepower, curb weight, drivetrain and body style.
- Benchmark within comparable segments. Compare cars of the same make, similar specifications and price positioning instead of a market-wide average.
- Evaluate price-segment performance separately. Measure error across budget, mid, upper and luxury segments to see where estimates are least dependable.
- Control overfitting. Use repeated cross-validation and regularisation; a single held-out test set is unreliable with only 14 luxury cars.
- Validate engineered features. Test whether average MPG, volume proxy and power-to-weight improve out-of-sample accuracy; do not use a price-derived brand flag.
- Handle rare categories cautiously. Do not use prices of rare car types, or of makes with 1–3 cars, as dependable benchmarks.
- Strengthen real-world pricing. Add vehicle age, mileage, mechanical condition, service and accident history, location and transaction date where available.

## 13. Conclusion

The EDA shows that automobile prices reflect a combination of brand positioning, engine capability, vehicle size and configuration; fuel efficiency is associated with price mainly through car class. Correlation analysis, corrected categorical tests and multicollinearity checks give a sound basis for regression modelling.

The next step is to establish which features deliver reliable predictive value through cross-validation on unseen data, and to present the output as a reference price with a stated range and limitations. See Automobile_Price_ML.ipynb for that modelling stage (cross-validated mean error 9.3%).
