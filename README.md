# day-ahead-price-prediction
price forecast in renewables

# Outline
In this folder you will find the following notebooks (numbered)
* **Data Analysis**:
  * EDA (this notebook)        
  * Outlier Analisys        
  * Feature Engineering

* **Non sequence Models**:
  * Baseline Model (historical average) and Linear Regression
  * XGBoost & lighGBM (and stacked models)
* **Sequence Models**
  * LSTMs
  * CNN-LSTMs
  * BiLSMTs
* **Sequence + Attention**
  * Transformed Based
  *. Transformed Based II (with engineered features)
*   **Extra**:
    * test01: ARIMA
    * test02: fb prophet
 
# Dataset
* 1 file, no need to merge datasets
* 43,847 rows and 8 columns.
* No NaNs
* **Timestamps** (contract-delivery, day-ahead-auction-time) are in object format (strings). We should convert them to datetime.
  * Did not use ~~day-ahead-auction-time~~.
  * Hourly data
  * Initial Date: 2020-02-05 01:00:00+00:00
  * Final Date: 2024-12-31 22:00:00+00:00
  * ✅  Total Missing Timestamps: 0
* **Features**:
  * demand-forecast, temperature-normal, temperature-forecast, solar-forecast, wind-forecast are **numeric variables**.
  * 🔴 **solar-forecast** has many zeros, likely due to nighttime hours?
  * 🔴 **solar-forecast**, **wind-forecast**, **day-ahead-auction-price** are left-skewed, could be transformed to Gaussian
* **Target variable (day-ahead-auction-price)**
    * 🔴 has extreme **outliers**
    * **periodicity**: clear trends for day, week, month, year
    * **negative values**: Shutting down production might be costlier than selling at a negative price.
    * negatively correlated with renewables forecast
    * moderately correlated with demand
 
# Results

| Model                                            | Mae   | Rmse  |
| :----------------------------------------------- | :---- | :---- |
| Historical Average                               | 30.37 | 40.85 |
| Initial Linear Regression                        | 17.7  | 23.81 |
| Improved Linear Regression (Feature Engineering) | 16.24 | 21.42 |
| XGBoost (Default Settings)                       | 15.12 | 20.06 |
| Tuned XGBoost (Hyperparameter Search)            | 14.86 | 19.73 |
| Stacked Model (XGBoost + LightGBM + Ridge)       | 15.33 | 20.39 |
| lightGBM (tuned)                                 | 15.93 | 20.81 |
| Stacked Model with tuned LGBM                    | 15.47 | 20.41 |
| LSTMs                                            | 11.74 | 15.19 |
| CNN-LSTM                                         | 12.27 | 17.58 |
| BiLSTM                                           | 9.82  | 14.61 |
| Transformer Model                                | 10.5  | 16.87 |
| **Improved Transformer (Feature Engineering)**   | **0.61**  | **6.91**  |

 
# Further Improvements:
* **Merge with other Data Sources**. Looking at the data, it is very probable that with just that dataset it is not possible to improve further the models. We could improve predictions by including extra data such as:

   * Country/Region
   * Holidays
   * Special events (dates)
   * Fuel prices
   * Economic indicators (inflation & interest rates, GDP growth)


* Other Models to try:
  * Statistical methods + Clustering
  *  Temporal Fusion Transformers
  *  DART temporal transformers
  *  pyTorch.forecasting
  *  Price Data Transformation: BoxCox Transform: convert to Gaussian pdf 
