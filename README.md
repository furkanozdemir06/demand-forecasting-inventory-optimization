# Demand Forecasting and Inventory Optimization

A data-driven pipeline that forecasts future customer demand with a seasonal time series model (SARIMA) and uses the forecast to compute inventory policy values: order quantity, reorder point, safety stock, and total cost.

## Overview

Companies need to keep enough stock to meet customer demand without paying for excess inventory or losing sales to stockouts. This project connects the two halves of that problem:

1. **Demand forecasting:** predict upcoming demand from historical sales.
2. **Inventory optimization:** turn the forecast into concrete inventory decisions under a target service level and holding/stockout costs.

## Dataset

The dataset (`demand_inventory.csv`) contains 62 daily records for a single product (`P1`), from 2023-06-01 to 2023-08-01.

| Column | Description |
|--------|-------------|
| `Date` | Daily date |
| `Product_ID` | Product identifier |
| `Demand` | Units demanded that day |
| `Inventory` | Inventory level that day |

Demand ranges from 51 to 199 units per day, with a mean of about 121.

## Workflow

1. **Exploratory data analysis**
   - Checked shape, data types, missing values (none), summary statistics, and correlations.
   - Dropped the redundant `Unnamed: 0` index column.
   - Plotted demand and inventory over time with Plotly.
2. **Demand forecasting**
   - Converted `Date` to a datetime index and differenced the demand series.
   - Used ACF and PACF plots of the differenced series to guide model selection.
   - Fitted `SARIMAX(order=(1,1,1), seasonal_order=(1,1,1,2))` and forecasted the next 10 days.
3. **Inventory optimization**
   - Set the business parameters: initial inventory 5,500 units, lead time 1, service level 95%, holding cost 0.1 per unit, stockout cost 10 per unit.
   - Computed the safety buffer from the 5th percentile (1 - service level) of the forecasted demand.
   - Derived order quantity, reorder point, safety stock, and total cost.

## Results

**10-day demand forecast:** roughly 114 to 130 units per day, alternating between lower and higher values (a pattern that follows the seasonal period of 2).

**Inventory policy:**

| Metric | Value |
|--------|-------|
| Optimal order quantity | 236 units |
| Reorder point | 235.25 units |
| Safety stock | 114.45 units |
| Total cost | 561.80 |

The total cost consists entirely of holding cost (0.1 x (5,500 + 0.5 x 236) = 561.80). The stockout cost is zero because the starting inventory of 5,500 units is far above the forecasted demand over the lead time.

## Tech Stack

- Python
- pandas, NumPy
- statsmodels (SARIMAX, ACF/PACF)
- Plotly
- matplotlib
