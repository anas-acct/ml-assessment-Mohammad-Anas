# Business Case Analysis

## B1. Problem Formulation

### (a)
This can be formulated as a **supervised machine learning regression problem**.

- **Target Variable:**  
  `items_sold` (number of items sold per store per month)

- **Input Features:**  
  - Store attributes: store_size, location_type  
  - Promotion details: promotion_type  
  - Temporal features: month, seasonality  
  - External factors: competition_density  
  - Calendar features: is_weekend, is_festival  

- **Problem Type:**  
  This is a **regression problem** because the goal is to predict a continuous numerical value (items sold).

---

### (b)
Using **items sold (sales volume)** is more reliable than revenue because:

- Revenue can be affected by pricing strategies (discounts reduce revenue but increase volume)
- Different promotions impact price differently, making revenue inconsistent
- Items sold directly reflects **customer response to promotions**

**Broader Principle:**  
The target variable should directly align with the **business objective**. In this case, the goal is to maximize product movement (sales volume), not necessarily revenue.

---

### (c)
Instead of one global model, a better approach is:

- **Segmented Models (Cluster-based or location-based models)**

For example:
- Separate models for urban, semi-urban, and rural stores  
OR  
- Use clustering to group similar stores and train models per cluster  

**Justification:**  
Different store types respond differently to promotions. A single model may average out these differences and reduce prediction accuracy.

---

## B2. Data and EDA Strategy

### (a)
The four tables would be joined as follows:

- **transactions table** → base table (contains sales data)
- Join with **store attributes** using `store_id`
- Join with **promotion details** using `promotion_id`
- Join with **calendar table** using `transaction_date`

**Grain of final dataset:**  
One row = **store × month × promotion**

**Aggregations:**
- Total items_sold per store per month
- Average basket size
- Monthly visits
- Promotion applied in that month

---

### (b)
EDA steps:

1. **Promotion vs Sales Analysis (Bar Chart)**  
   - Compare average items sold across promotion types  
   - Helps identify which promotions perform better  

2. **Time Series Trend (Line Plot)**  
   - Monthly sales trends  
   - Detect seasonality and trends  

3. **Correlation Heatmap**  
   - Identify relationships between variables  
   - Helps in feature selection  

4. **Distribution of Items Sold (Histogram)**  
   - Understand skewness and outliers  
   - Decide if transformation is needed  

**Impact on modeling:**  
- Strong correlations → useful features  
- Seasonality → add time-based features  
- Skewed data → apply transformations  

---

### (c)
If 80% transactions have no promotion:

**Impact:**
- Model may become biased toward "no promotion"
- Poor learning of promotion effects

**Solutions:**
- Use stratified sampling  
- Apply resampling techniques  
- Add class weights or focus on promotion-specific subsets  

---

## B3. Model Evaluation and Deployment

### (a)
**Train-test split:**
- Use first 80% (earlier time) as training data  
- Last 20% (recent data) as test set  

**Why not random split:**
- Random split causes data leakage  
- Future data may influence past predictions  

**Evaluation Metrics:**
- RMSE: Measures overall prediction error (penalizes large errors)
- MAE: Measures average absolute error (easy to interpret)

**Interpretation:**
Lower RMSE and MAE indicate better model performance and more accurate sales predictions.

---

### (b)
To explain different recommendations:

- Use **feature importance** from the model  
- Compare features for December vs March  

For example:
- December: festivals, high demand → Loyalty Points works better  
- March: lower demand → Flat Discount drives more sales  

**Communication:**
Explain to marketing that:
- Model decisions are based on seasonality, promotions, and store conditions  
- Different months have different customer behavior patterns  

---

### (c)
**Deployment Process:**

1. **Save model:**
   - Use pickle or joblib to save trained pipeline  

2. **Data preparation:**
   - Collect new monthly data  
   - Apply same preprocessing pipeline  

3. **Prediction:**
   - Model predicts items sold for each promotion  
   - Select best promotion per store  

4. **Monitoring:**
   - Track RMSE/MAE over time  
   - Compare predicted vs actual sales  

5. **Retraining trigger:**
   - If performance degrades significantly  
   - Or new patterns emerge (seasonal or market changes)

This ensures the model remains accurate and useful over time.
