# Integrated-Retail-Analytics-for-Store-Optimization

An end-to-end retail analytics project that uses machine learning and data analytics to improve store performance, forecast demand, and drive personalized marketing and inventory strategies — built on three years (2010–2012) of weekly sales data across 45 stores and 81+ departments.

---

## Project Objective

Leverage machine learning and data analytics to improve store performance, accurately forecast demand, and elevate customer experience through targeted segmentation and personalized marketing strategies.

---

## Dataset

| Dataset | Rows | Key Fields | Grain |
|---|---|---|---|
| **Sales** | 421,570 | Store, Dept, Date, Weekly_Sales, IsHoliday | Store × Dept × Week |
| **Features** | 8,190 | CPI, Unemployment, Fuel_Price, Temperature, MarkDown1–5 | Store × Week |
| **Stores** | 45 | Store, Type (A/B/C), Size | Store |

All three datasets are merged on `Store` + `Date` (+ `IsHoliday`) into a single analytical dataframe used throughout the notebook.

---

## Project Components

1. **Exploratory Data Analysis (EDA)** — sales trends over time, top stores/departments, markdown patterns, and correlation between sales and external factors.
2. **Data Preprocessing & Feature Engineering** — median imputation for MarkDown nulls, standardization of Weekly_Sales, square-root transform to reduce skewness.
3. **Anomaly Detection** — statistical (mean/std, IQR) and ML-based (Isolation Forest) detection of unusual sales patterns, plus time-based detection using rolling statistics and EWMA.
4. **Customer Segmentation Analysis** — PCA + K-Means clustering of stores and departments, evaluated with the elbow method and silhouette score.
5. **Market Basket Analysis** — Apriori algorithm applied to department co-occurrence (as a proxy for baskets) to surface cross-sell associations.
6. **Demand Forecasting** — SARIMAX (short-term), Random Forest (long-term with exogenous features), and Holt-Winters exponential smoothing (2013 outlook).
7. **Impact of External Factors** — feature importance analysis and SARIMAX/Random Forest models incorporating CPI, unemployment, fuel price, and temperature as exogenous variables.
8. **Real-World Application & Strategy Formulation** — translating all of the above into inventory, marketing, and store-optimization recommendations.

---

## Key Findings

### EDA
- Weekly sales spike sharply in the two weeks before Christmas each year, driven by Black Friday and holiday promotions, then drop off in early January.
- The single highest-grossing week in the dataset was **24 Dec 2010 ($80.9M)**.
- **Store #20 and #4** are the top earners; **Store #44, #33, #3, #5** are the least profitable.
- **Store Type A** earns the most overall, followed by Type B, then Type C — though Types A/B show a slight annual decline (~5%) while Type C grows steadily (~3%/yr).
- Departments **1–15, 38, 40, 72, and 90–95** post ~25% higher weekly sales than the average department.
- Markdown activity rises sharply before major holidays; MarkDown features are right-skewed with 5–8% outliers, so median imputation was used instead of mean.
- Weekly Sales show weak direct correlation with CPI, unemployment, or fuel price; CPI and Unemployment are negatively correlated with each other, as are Unemployment and Fuel Price.

### Anomaly Detection
- **415,415** records (98.5%) classified as normal, **6,155** (1.5%) flagged as anomalies.
- Holidays are the dominant driver of unusual spikes: on non-holiday weeks, ~90% of records skew toward lower sales; during holiday weeks, anomalies skew toward much higher sales.
- Rolling-statistics and EWMA views confirm sales trend roughly flat over time, with anomalies tied to markdown events, seasonal shifts, and holidays.

### Segmentation
- **Store segmentation:** optimal **k = 4** (silhouette score 0.669) across 4 clusters — Premium Space Retailers, Value-Oriented Stores, Budget-Friendly Stores, and Compact Elegance Stores — sized 15, 18, 4, and 8 stores respectively.
- **Department segmentation:** optimal **k = 5** (silhouette score 0.406) across 81 departments, yielding profiles from "Sizeable Luxury Departments" to "Elite Departments" with distinct average weekly sales.

### Market Basket Analysis
- Top association rules (by confidence) reach up to **100% confidence** for select department pairings (e.g., a 29-department group → Department 30), supporting store-layout co-location and bundled cross-department promotions.

### Demand Forecasting
| Model | MAE ($) | RMSE ($) |
|---|---|---|
| SARIMAX (short-term) | 10,497 | 12,276 |
| Random Forest (long-term) | 1,933 | 3,719 |
| Random Forest + external factors (CPI % change, temp, fuel) | 2,456 | 5,051 |

- Random Forest substantially outperforms SARIMAX, driven primarily by 1-week and 2-week lagged sales features.
- Seasonal decomposition confirms sales are strongly influenced by the seasonal component; the Holt-Winters model was used to project sales into 2013.

### Impact of External Factors
- Feature importance from the Random Forest model: **lagged sales (1wk + 2wk) ≈ 86%** combined importance; **Temperature ≈ 5.6%** is the top pure external factor, followed by CPI (2.3%), Unemployment (1.9%), and Fuel Price (1.6%).
- Adding external/exogenous features did not outperform the lag-driven baseline Random Forest model — recent sales momentum remains the strongest predictor.

---

## Strategy Recommendations

**Inventory Management**
- Use temperature data to forecast seasonal demand and allocate inventory accordingly.
- Apply CPI-based dynamic pricing — discount during low-inflation periods.
- Monitor fuel prices to optimize supply chain routes and distribution costs.

**Marketing Strategies**
- Tailor campaigns to regional climate (e.g., winter gear vs. summer gear promotions).
- Use CPI-based price-sensitivity analysis to guide offers.
- Time promotions around periods of low fuel prices to offset dips in discretionary spending.

**Store Optimization**
- Arrange product placement to reflect seasonal demand patterns.
- Use CPI data to optimize inventory turnover and reduce carrying costs.
- Offer convenience-oriented services (e.g., flexible delivery) to offset fuel-price-driven cost sensitivity.

**Implementation Challenges**
- **Data Integration** — unifying temperature, CPI, and fuel-price sources into existing systems.
- **Resource Allocation** — staffing and tooling for real-time dynamic pricing and targeted campaigns.
- **Operational Flexibility** — adapting supply chains quickly to external shifts.
- **Competitive Pressure** — balancing pricing changes against market dynamics.

---

## Tech Stack

- **Python**: pandas, numpy, matplotlib, seaborn
- **Machine Learning**: scikit-learn (KMeans, PCA, IsolationForest, RandomForestRegressor, silhouette_score)
- **Time Series**: statsmodels (SARIMAX, Holt-Winters ExponentialSmoothing, seasonal_decompose)
- **Association Rule Mining**: apyori (Apriori algorithm)

---

## Repository Contents

- `Integrated_Retail_Analytics.ipynb` — full analysis notebook (EDA → preprocessing → anomaly detection → segmentation → market basket → forecasting → strategy)
- `Integrated_Retail_Analytics_Presentation.pptx` — 25-slide summary presentation with charts, key numbers, and findings
- `README.md` — this file

---

## How to Run

1. Open `Integrated_Retail_Analytics.ipynb` in Jupyter or Google Colab.
2. Update the data file paths (the notebook was originally built against Google Drive-mounted CSVs — swap in local paths for `Features data set.csv`, `sales data-set.csv`, and `stores data-set.csv`).
3. Run all cells in order — later sections (segmentation, forecasting) depend on dataframes created earlier in the notebook.
