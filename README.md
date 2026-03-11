# Sales-Forecasting---Rossmann-Dataset
Rossmann Sales Forecasting using CatBoost. Features store-level vs. global aggregate modeling with time-series validation. Implements lag/momentum feature engineering, grid search tuning, and residual diagnostics. Validated via target shuffling to ensure zero data leakage and high model integrity.

Sales Forecasting - Rossmann Dataset

1.  Objectives

Two forecasting tasks were implemented:

- Store-Level Forecasting – Predict daily sales for Store 262
- Global Forecasting – Predict aggregated average daily sales across all open stores

Both tasks used a consistent time-aware evaluation framework.

This project used the Rossmann Store Sales dataset (https://www.kaggle.com/competitions/rossmann-store-sales/data), which was originally provided for a Kaggle competition in 2015.
train.csv, which contains daily sales records from January 2013 to July 2015 for over 1,000 stores
store.csv, which provides extra information about each store such as store type, assortment, and competition details.


2. Model Selection

CatBoost was selected because:

- Strong performance on structured tabular data
- Native categorical feature handling
- Robust to missing values
- Effective with mixed numerical & categorical features
- No one-hot encoding was required.


3. Objective 1: Store-Level Forecasting (Store 262)

3.1 Data Setup
- Trained only on Store 262 data
- Closed days and zero-sales days removed
- Time-based train/test split:
- Train: up to June 1, 2015
- Test: after June 1, 2015
- 15% of training data used for validation
- Early stopping enabled

3.2 Features Used
- Temporal Features
- DayOfWeek
- Month
- Year
- WeekOfYear
- IsWeekend

3.3 Lag Features
- Lag1
- Lag7
- Rolling7

3.4 Categorical Features
- Promo
- StateHoliday
- SchoolHoliday
- StoreType
- Assortment
- Customers was intentionally removed to prevent target leakage.

3.5 Hyperparameter Tuning (Grid search):
- Iterations: 500, 1000
- Learning rate: 0.05, 0.1
- Depth: 4, 6, 8
- Early stopping based on validation loss.

3.6 Validation Check – Target Shuffling
- Target values were randomly shuffled
- Model retrained
- Performance dropped significantly
- This confirmed the model was learning meaningful signal and not leakage.

3.7 Results – Objective 1
- Model captured overall sales trend
- Persistent over-forecasting bias
- Residuals consistently negative
- Over-forecast range: approx. -250 to -2000

3.8 Feature Importance (Top Drivers):
- DayOfWeek (dominant feature)
- StateHoliday
- IsWeekend
- Month / WeekOfYear

3.9 Insight:
- Calendar effects were significantly more predictive than store-specific attributes or competition features.
- Systematic over-forecasting reduces suitability for operational decisions (inventory, staffing).


4. Objective 2: Global Average Sales Forecasting

4.1 Data Setup
- Sales aggregated per date (average across open stores)
- Store-level categorical features removed
- Same time-based split strategy as Objective 1

4.2 Temporal Features
- DayOfWeek
- WeekOfYear
- Month
- Year
- Linear time index

4.3 Lag & Trend Features
- Lag1
- Lag7
- Rolling7
- Rolling14
- SalesDiff1
- SalesDiff7

4.4 Results – Objective 2
- Strong overall trend capture
- Residuals centered around zero
- No systematic bias
- Most residuals within ±200
- Some volatility on extreme spike days

4.5. Feature Importance – Objective 2
Most Important:
- SalesDiff7
- SalesDiff1

Low Importance:
- Rolling averages
- Calendar features
- Linear time trend

4.6. Insight:
Recent momentum (daily & weekly changes) is the strongest driver of aggregated sales.


5. Key Takeaways of the entire project
- Global-level has much healthier residual distribution
- There is no consistent over- or under-forecasting for global-level forecasting
- Store-level forecasting is harder and more prone to bias
- Aggregated forecasting reduces noise and improves stability
- Calendar effects dominate single-store sales
- Momentum features dominate global sales
- Target shuffling validated absence of leakage


6. What This Demonstrates
- Time-series aware model evaluation
- Prevention of data leakage
- Practical hyperparameter tuning
- Residual diagnostics & bias analysis
- Feature importance interpretation
- Handling of real-world hardware constraints
- Structured ML pipeline design
