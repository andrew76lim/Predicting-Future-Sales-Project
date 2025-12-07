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

**Mean Envodings**
| Name               | Role    | Level       | Description                                      |
|--------------------|---------|-------------|--------------------------------------------------|
| `lagged_it_mean`   | Feature | Continuous  | Cumulative mean sales per item                   |
| `lagged_sh_it_mean`| Feature | Continuous  | Cumulative mean sales per shop item pair         |

**Lag Features**


## Test Data


**Citations:**

https://www.kaggle.com/code/faressayah/data-science-best-practices-with-pandas-2-0-p2/notebook
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement/input
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement
https://www.kaggle.com/code/lakshtanov18031987/predict-future-sales-0-86-solution/input
