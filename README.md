# Bitcoin Price Analysis and Forecasting

This repository contains a comprehensive analysis and forecasting project for Bitcoin prices. Using Python libraries such as Plotly for visualization and AutoTS for automated time series forecasting, this project visualizes historical Bitcoin price data and predicts future trends.

## Table of Contents
- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)

## Overview
This project aims to:
1. Visualize historical Bitcoin prices using candlestick charts.
2. Analyze the correlation between different price metrics.
3. Forecast future Bitcoin prices using the AutoTS library.

## Installation
1. Clone the repository:
    ```bash
    git clone https://github.com/your-username/Bitcoin-Price-Analysis-and-Forecasting.git
    cd Bitcoin-Price-Analysis-and-Forecasting
    ```
2. Create and activate a virtual environment (optional but recommended):
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
3. Install the required packages:
    ```bash
    pip install -r requirements.txt
    ```

## Usage
1. Ensure your Bitcoin price data is in a CSV file with columns: `Date`, `Open`, `High`, `Low`, `Close`.
2. Update the `data.csv` path in the notebook or script if necessary.
3. Run the Jupyter Notebook or Python script to generate visualizations and forecasts.

### Example
```python
import plotly.graph_objects as go
import pandas as pd
from autts import AutoTS

# Load data
data = pd.read_csv('path_to_your_data.csv', parse_dates=['Date'])

# Create candlestick chart
figure = go.Figure(data=[go.Candlestick(x=data["Date"],
                                        open=data["Open"],
                                        high=data["High"],
                                        low=data["Low"],
                                        close=data["Close"])])
figure.update_layout(title="Bitcoin Price Analysis",
                     xaxis_rangeslider_visible=False)
figure.show()

# Calculate correlation
correlation = data.corr()
print(correlation["Close"].sort_values(ascending=False))

# Prepare data for AutoTS
data = data[['Date', 'Close']].rename(columns={'Date': 'date', 'Close': 'value'})

# Initialize and fit AutoTS model
model = AutoTS(forecast_length=30, frequency='infer', ensemble='simple')
model = model.fit(data, date_col='date', value_col='value', id_col=None)

# Forecast future prices
prediction = model.predict()
forecast = prediction.forecast
print(forecast)

# Plot forecast
forecast_dates = forecast.index
figure = go.Figure()
figure.add_trace(go.Scatter(x=data['date'], y=data['value'], mode='lines', name='Historical'))
figure.add_trace(go.Scatter(x=forecast_dates, y=forecast['value'], mode='lines', name='Forecast'))
figure.update_layout(title="Bitcoin Price Forecast", xaxis_title='Date', yaxis_title='Price')
figure.show()
