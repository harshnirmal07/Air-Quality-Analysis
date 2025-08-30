# Air-Quality-Analysis

A time-series project that predicts key air-pollutant levels for New Delhi and explores their inter-relationships.  
It combines thorough data preprocessing with an LSTM model to deliver accurate forecasts and clear insights on urban air quality.

---

## 1. Introduction
Air quality is a critical determinant of public health and environmental well-being. Rapid urbanisation and industrial growth make continuous monitoring essential for data-driven policy and informed decision-making.  
This repository:

- Ingests historical measurements from OpenAQ.  
- Cleans, reshapes, and normalises the data.  
- Trains an LSTM to predict six pollutant metrics simultaneously.  
- Evaluates performance with robust cross-validation.  
- Visualises both ground-truth and predicted trends.

---

## 2. Dataset

| Field         | Unit                 | Description                                             |
|---------------|----------------------|---------------------------------------------------------|
| `PM1`         | µg/m³                | Fine particulate matter < 1 µm                          |
| `PM2.5`       | µg/m³                | Fine particulate matter < 2.5 µm                        |
| `PM10`        | µg/m³                | Particulate matter < 10 µm                              |
| `RH`          | %                    | Relative humidity                                       |
| `Temperature` | °C                   | Ambient temperature                                     |
| `PM0.3`       | particles / cm³      | Ultra-fine particle count < 0.3 µm                      |

- **Source:** [OpenAQ](https://openaq.org) API (station: New Delhi, GK1 – Oberoi Terrace).  
- **Date range:** 1 Nov 2024 to 12 Dec 2024.

---

## 3. Data preprocessing

1. **Load and trim**  
   - Drop non-analytical columns (`country_iso`, `isMobile`, `isMonitor`).  
2. **Reshape**  
   - Build a pivot table so each timestamp has all pollutant readings in one row.  
3. **Missing-value check**  
   - Verify integrity; impute or drop if required.  
4. **Train-test split**  
   - Training: before 5 Dec 2024.  
   - Testing: 5 Dec 2024 onward.  
5. **Normalisation**  
   - Apply MinMaxScaler to map every feature to `[0, 1]`.

---

## 4. Model architecture

| Layer            | Purpose                                   |
|------------------|-------------------------------------------|
| Input            | 3 time steps × 6 features                 |
| LSTM × 3         | Capture temporal dependencies             |
| Dropout after each LSTM | Reduce overfitting                  |
| Dense            | Map LSTM output to six simultaneous targets |

**Hyperparameters**

- Epochs: 1000  
- Batch size: 32  
- Optimiser: Adam, lr = 0.001  

**Callbacks**

- `EarlyStopping` stops when validation loss plateaus.  
- `ReduceLROnPlateau` lowers learning rate when improvements stall.

**Cross-validation**

- `TimeSeriesSplit` ensures the model is validated on multiple forward-rolling windows.

---

## 5. Evaluation metrics

| Metric | Meaning |
|--------|---------|
| MSE    | Average squared difference between predicted and actual values |
| MAE    | Average absolute difference between predicted and actual values |

Lower scores indicate better predictive performance.

---

## 6. Results and visualisations

- **Scatter and joint plots** show predicted vs true values; close alignment along the 45° line signals accuracy.  
- **Pair plots** reveal relationships among pollutants in the ground-truth data, confirming strong PM2.5–PM10 correlation.  
- **Heatmaps**  
  - True-value correlation highlights inverse temperature–RH relationship.  
  - Predicted-value correlation demonstrates that the model preserves these interdependencies.  

---

## 7. Key insights

- **Temporal learning**  
  - LSTM layers effectively model day-to-day and intra-day pollutant trends.  
- **Correlated pollutants**  
  - PM2.5 and PM10 track each other closely due to shared sources.  
- **Climate influence**  
  - Temperature and humidity exhibit an expected inverse link.  
- **Model robustness**  
  - Cross-validated training plus dropout regularisation curb overfitting and improve generalisation.

---
