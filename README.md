# Predicting Future Sales Project: Andrew Lim


## Basic Information
- **Person Developing Model**: Andrew Lim
- **Developer Email**: andrew76lim@gwmail.gwu.edu
- **Model Date**: December 6, 2025
- **Model Version**: V1.0
- **License**: MIT Liscense
- **Model Code**: https://colab.research.google.com/drive/1zeuvNyhlBMxWQtJwj2vzWaFM_4d1mkYT?usp=sharing


### **Intended Use**

**Intended Uses**
- Forecast the next month 'item_cnt_month' for each '(shop_id, item_id)' pair
- Select the best model using a validation split
- Produce predictions for the Kaggle data and evaluate through code with learning and inspiration from some of the leaderboard codes
- Demonstrate teh complete ML workflow for the project

**Intended Users**
- Professor Hall in the Data Ethics Class
- Machine learning practitioners for Kaggle
- Retail analysts

**Out of Scope Uses**
- Real time forecasting
- Automated financial or inventory decision making without need of human review
- Modeling promotions, elasticity, or macroeconomic conditions


## Training Data

**Source of Training Data**
- Kaggle Predict Future Sales data package
- item_categories.csv
- items.csv
- sales_train.csv
- sample_submission.csv
- shops.csv
- test.csv
- This is all stored in 'data/raw/'

**How Training Data was Divided Into Training and Validation Data**
- Using validation split for model selection, the data was divided chronologically

| Split      | Months (`date_block_num`) | Purpose                               |
|------------|---------------------------|---------------------------------------|
| Training   | 19–32                     | LightGBM model fitting                |
| Validation | 33                        | Model selection, early stopping       |
| Test       | 34                        | Kaggle leaderboard scoring            |

**Number of Rows in Training and Validation Data**
- After creating the matrix:
- Training rows = 2.4 to 2.7 million
- Validation rows = 214,000

**Training Data Dictionary**
| **Name** | **Modeling Role** | **Measurement Level** | **Description** |
|---------|-------------------|------------------------|-----------------|
| **date_block_num** | input | int | Month index ranging from 0 to 33 representing each month in the dataset |
| **shop_id** | input | int | Identifier for the shop, categorical feature representing store location/type |
| **item_id** | input | int | Identifier for each product, categorical feature |
| **month** | input | int | Calendar month extracted from `month_start` (1–12) |
| **days_in_m** | input | int | Number of unique selling days within the month for that shop–item combination |
| **item_cnt_month** | target | float | Monthly number of units sold for a given `(shop_id, item_id)`; clipped to [0, 20] for Kaggle submission |
| **item_cnt_month_uncl** | helper variable | float | Uncapped monthly sales count before clipping or adjustments |
| **revenue** | helper variable | float | Total item revenue, `item_price * item_cnt_day` aggregated by month |
| **purch_cnt_month** | helper variable | int | Count of days during which at least one unit was sold |
| **avg_price_mnth** | input | float | Average item price for the shop–item pair during the month |
| **avg_price_global** | input | float | Long-term global average price for each item across all months < 34 |
| **avg_price_mnth_lag1** | input | float | Lag 1 (previous month) average item price at the item level |
| **avg_price_mnth_lag2** | input | float | Lag 2 (two months prior) average item price at the item level |
| **avg_price_mnth_sh_lag1** | input | float | Lag 1 average price at the shop–item level |
| **avg_price_mnth_sh_lag2** | input | float | Lag 2 average price at the shop–item level |
| **avg_price_mnth_grad** | input | float | Price gradient ratio, lag1 / lag2, measuring direction of price movement |
| **avg_price_mnth_to_gl** | input | float | Ratio of current month’s price to global long-term average price |
| **avg_price_mnth_sh_grad** | input | float | Gradient of shop item price: lag1 / lag2 |
| **item_category_id** | input | int | Raw category identifier for the item |
| **item_category_type_encoded** | input | int | Encoded major category type derived from item category names |
| **item_category_subtype_encoded** | input | int | Encoded subcategory derived from item category names |
| **shop_city_encoded** | input | int | Encoded city parsed from the shop name |
| **shop_type_encoded** | input | int | Encoded type of shop (mall, market, specialized shop)       |
| **lagged_it_mean** | input | float | Cumulative mean sales per item up to the previous month |
| **lagged_sh_it_mean** | input | float | Cumulative mean sales per shop item pair up to the previous month |
| **item_cnt_month_sum_lag_1** | input | float | Shop item sales from 1 month ago |
| **item_cnt_month_sum_lag_2** | input | float | Shop item sales from 2 months ago |
| **item_cnt_month_sum_lag_3** | input | float | Shop item sales from 3 months ago |
| **item_cnt_month_sum_lag_6** | input | float | Shop item sales from 6 months ago |
| **item_cnt_month_sum_lag_12** | input | float | Shop item sales from 12 months ago |
| **item_cnt_month_mean_lag_1** | input | float | Item level mean sales from 1 month ago |
| **item_cnt_month_mean_lag_2** | input | float | Item level mean sales from 2 months ago |
| **item_cnt_month_mean_lag_3** | input | float | Item level mean sales from 3 months ago |
| **item_cnt_month_mean_shop_lag_1** | input | float | Shop level mean sales from 1 month ago |
| **item_cnt_month_mean_shop_lag_2** | input | float | Shop level mean sales from 2 months ago |
| **item_cnt_month_mean_shop_lag_3** | input | float | Shop level mean sales from 3 months ago |
| **item_cnt_month_item_category_id_mean_lag_1** | input | float | Category level mean sales from 1 month ago |
| **item_cnt_month_item_category_id_mean_lag_2** | input | float | Category level mean sales from 2 months ago |
| **item_cnt_month_item_category_id_mean_lag_3** | input | float | Category level mean sales from 3 months ago |


## Test Data

**Sources**
- 'test.csv' from Kaggle

**Number of Rows**
- 214,200 rows

**Differences from Training Data**
| Aspect              | Training Data                  |
|---------------------|--------------------------------|
| Time Index          | 'date_block_num' 0-33          |
| Target              | Present ('item_cnt_month')     |
| Engineered Features | Created from history           |
| IDs                 | No 'ID' column                 |

**Kaggle Submission**
- Final file: 'submission-15.csv'
- Public leaderboard RMSE: 0.95860
- Public Team File: ANDREWLIM


## Model Details

**Columns used as inputs in the final model**
- All features originally included are used except:
- 'ID'
- 'item_cnt_month'
- 'item_cnt_month_uncl'
- 'revenue'
- 'purch_cnt_month'
- 'avg_price_month'
- These are a mix of targets, leakage, or fields not used in the training

**Target in the Final Month**
- 'item_cnt_month'
-  The target is capped to [0,20]

**Type of Model**
- Gradient boosted decision trees using LightGBM

**Software Used**
- Python 3.10
- Pandas 2.0
- Numpy 1.26
- LightGBM 4
- Scikit-learn 1.5
- Seaborn 0.13
- Matplotlib 3.8

**Version of Modeling Software**
- LightGBM v4.x

**Hyperparameters**
- yaml
- Objective: MSE
- Metric: RMSE
- Num_leaves: 255
- Learning_rate: 0.005
- Feature_fraction: 0.75
- Bagging_fraction: 0.75
- Bagging_freq: 5
- Force_col_wise: true
- Random_state: 10
- Num_boost_round: 1500
- Early_stopping_rounds: 100
- The model is trained with early stopping using month-33 validation


## Quantitative Analysis

**Metrics Used**
- Since this is a regression task, the root mean squared error (RMSE): which is also what is scored on the competition

**Final Values (Training, Validation, Test)**
| Dataset     | RMSE       |
|-------------|------------|
| Training    | 0.739629   |
| Validation  | 0.777630   |
| Kaggle      | 0.96255    |
- The higher Kaggle RMSE reflects the more difficult distribution of the held-out test set

**Different Plots**
1. Monthly Sales Trend
<img width="625" height="308" alt="Screenshot 2025-12-07 at 8 50 38 PM" src="https://github.com/user-attachments/assets/a1d28f14-c397-4eda-b332-2675dec198fe" />

2. LightGBM Feature
<img width="633" height="618" alt="Screenshot 2025-12-07 at 8 51 24 PM" src="https://github.com/user-attachments/assets/f73153cf-61dd-43d8-9ead-e63959d6843c" />

3. Actual vs Predicted Validation
<img width="613" height="590" alt="Screenshot 2025-12-07 at 8 51 41 PM" src="https://github.com/user-attachments/assets/f3a781bc-dcf1-41ac-ae80-25e60ef9570a" />

4. Validation Error Distribution
<img width="614" height="313" alt="Screenshot 2025-12-07 at 8 52 06 PM" src="https://github.com/user-attachments/assets/e9e59301-1dac-49c2-9720-ff075ab085ac" />

5. Heatmap for Key Factors
<img width="603" height="509" alt="Screenshot 2025-12-07 at 8 52 42 PM" src="https://github.com/user-attachments/assets/c67e0cd3-6ede-4fdd-9ffc-1d5b4aaf7b34" />


## Ethical Considerations

### Potential Negative Impacts

**Mathematical and Software Risks**
- Overfitting to historical lags when the market shifts
- Sensitivity to outliers distorting price gradient features
- Few items producing unstable lag signals

**Real World Risks**
- Who: inventory planners using forecast
- What: incorrect forecasts can result in either shortages or excess inventory
- When: seasonality changes, economic shocks, and supply chain disruptions
- How: over reliance on point predicitions

### Potential Uncertainties

**Mathematical and Software Uncertainties**
- Having inconsistencies in retail demand
- Encoded category and shop fields becoming different over time
- Hyperparameter sensitivity and variance in boosted models

**Real World Uncertainties**
- Different forms of promotions, weather, macoeconomic conditions not included in the model
- Risk of decision makers ot understanding the limits of the model

### Unexpected Results
- Price gradients were more predictive than raw prices
- Caregory subtype encodings had strong predictive power
- Very long lags of greater than 6 months had limited value which confirms short term recency is most important



## Citations:

https://www.kaggle.com/code/faressayah/data-science-best-practices-with-pandas-2-0-p2/notebook
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement/input
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement
https://www.kaggle.com/code/lakshtanov18031987/predict-future-sales-0-86-solution/input
