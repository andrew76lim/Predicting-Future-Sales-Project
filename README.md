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

**Citations:**

https://www.kaggle.com/code/faressayah/data-science-best-practices-with-pandas-2-0-p2/notebook
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement/input
https://www.kaggle.com/code/sangseoseo/predictfuturesalesperformanceenhancement
https://www.kaggle.com/code/lakshtanov18031987/predict-future-sales-0-86-solution/input
