# Predicting Future Sales Project: Andrew Lim

## Basic Information
- **Person Developing Model**: Andrew Lim
- **Developer Email**: andrew76lim@gwmail.gwu.edu
- **Model Date**: December 6, 2025
- **Model Version**: V1.0
- **License**: MIT Liscense
- **Model Code**: https://colab.research.google.com/drive/1fGBhN3r65Vyl-gLQ1WS3yeXP1E8OJ78c?usp=sharing

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

**Data Dictionary**

**Identifier and Calendar Columns**
| Name             | Role        | Level       | Description                                |
|------------------|-------------|-------------|--------------------------------------------|
| `date_block_num` | Feature     | Ordinal     | Month index (0–34)                         |
| `shop_id`        | Feature     | Categorical | Store ID                                   |
| `item_id`        | Feature     | Categorical | Item ID                                    |
| `month`          | Feature     | Categorical | Calendar month (1–12)                      |
| `days_in_m`      | Feature     | Integer     | Number of selling days in that month       |

**Target Variables**
| Name                 | Role        | Level       | Description                                             |
|----------------------|-------------|-------------|---------------------------------------------------------|
| `item_cnt_month`     | Target      | Continuous  | Monthly sales per `(shop_id, item_id)`, capped [0,20].  |
| `item_cnt_month_uncl`| Helper      | Continuous  | Uncapped total monthly `item_cnt_day`.                  |

**Price Features**
| Name                      | Role    | Level       | Description                                     |
|---------------------------|---------|-------------|-------------------------------------------------|
| `avg_price_mnth`          | Feature | Continuous  | Monthly average item price                      |
| `avg_price_global`        | Feature | Continuous  | Long run global average price                   |
| `avg_price_mnth_lag1`     | Feature | Continuous  | Item lag 1 price                                |
| `avg_price_mnth_lag2`     | Feature | Continuous  | Item lag 2 price                                |
| `avg_price_mnth_sh_lag1`  | Feature | Continuous  | Shop item lag 1 price                           |
| `avg_price_mnth_sh_lag2`  | Feature | Continuous  | Shop item lag 2 price                           |
| `avg_price_mnth_grad`     | Feature | Continuous  | Price gradient (item lag1 / lag2)               |
| `avg_price_mnth_to_gl`    | Feature | Continuous  | Current price / global average price            |
| `avg_price_mnth_sh_grad`  | Feature | Continuous  | Shop item price gradient                        |

**Category and Shop Data**
| Name                            | Role    | Level       | Description                                   |
|---------------------------------|---------|-------------|-----------------------------------------------|
| `item_category_id`              | Feature | Categorical | Raw category ID                               |
| `item_category_type_encoded`    | Feature | Categorical | Encoded high level category type              |
| `item_category_subtype_encoded` | Feature | Categorical | Encoded fine grained subtype                  |
| `shop_city_encoded`             | Feature | Categorical | Encoded city extracted from shop name         |
| `shop_type_encoded`             | Feature | Categorical | Encoded shop type                             |

**Mean Encodings**
| Name               | Role    | Level       | Description                                      |
|--------------------|---------|-------------|--------------------------------------------------|
| `lagged_it_mean`   | Feature | Continuous  | Cumulative mean sales per item                   |
| `lagged_sh_it_mean`| Feature | Continuous  | Cumulative mean sales per shop item pair         |

**Lag Features**
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


**Citations:**

https://www.kaggle.com/code/faressayah/data-science-best-practices-with-pandas-2-0-p2/notebook
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement/input
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement
https://www.kaggle.com/code/lakshtanov18031987/predict-future-sales-0-86-solution/input
