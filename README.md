# inflation Impact Analysis using Python

## Introduction: 

Definition of Inflation: Inflation is a general increase in prices over time.
Impact of Inflation: It affects purchasing power, consumer behavior, savings, and investment.
Moderate Inflation: Can be beneficial for economic growth.
High Inflation: Can be detrimental, eroding purchasing power and causing economic uncertainty.
Exchange Rate Relationship: Countries with higher inflation tend to have weaker currencies compared to countries with lower inflation.
Data Analysis Plan: The author intends to use a dataset containing inflation and exchange rate data for India and the US to analyze the relationship between these variables.
The analysis will likely involve time series analysis, correlation analysis, and regression analysis to identify trends, relationships, and potential causal effects.

## Inflation Impact Analysis with Python

We have two data files for this task. Let’s start the task of Inflation impact analysis by importing the necessary Python libraries and the datasets:

import pandas as pd
import plotly.express as px
import plotly.graph_objs as go

inflation_data = pd.read_csv('Inflation_Rates_Transformed.csv')
exchange_rate_data = pd.read_csv('USD_INR_Exchange_Rates_1980_2024.csv')

exchange_rate_data.head()

## Merging the relevant parts of these datasets to analyze the impact of inflation on exchange rates

# filter the inflation data for India and the United States
inflation_filtered_df = inflation_data[inflation_data['Country'].isin(['India', 'United States'])]

# pivot the inflation data to have separate columns for India and the United States inflation rates
inflation_pivot_df = inflation_filtered_df.pivot(index='Year', columns='Country', values='Inflation Rate').reset_index()

# merge the exchange rates data with the inflation data
merged_df = pd.merge(exchange_rate_data, inflation_pivot_df, on='Year')

# renaming columns
merged_df.columns = ['Year', 'Exchange Rate (INR/USD)', 'Inflation Rate (India)', 'Inflation Rate (United States)']

merged_df.head()

The merged dataset now contains the following columns for each year:

Exchange Rate (INR/USD): The average exchange rate of INR to USD.
Inflation Rate (India): The inflation rate for India.
Inflation Rate (United States): The inflation rate for the United States.

## Let’s start by analyzing the trend of inflation rates for both India and the United States alongside the exchange rate.

import plotly.graph_objs as go
from plotly.subplots import make_subplots

fig = make_subplots(rows=3, cols=1,
                    shared_xaxes=True,
                    vertical_spacing=0.1,
                    subplot_titles=("Trend of Exchange Rate (INR/USD)",
                                    "Trend of Inflation Rate (India)",
                                    "Trend of Inflation Rate (United States)"))

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Exchange Rate (INR/USD)'],
                         mode='lines+markers',
                         marker=dict(color='blue'),
                         name='Exchange Rate (INR/USD)'),
              row=1, col=1)

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Inflation Rate (India)'],
                         mode='lines+markers',
                         marker=dict(color='orange'),
                         name='Inflation Rate (India)'),
              row=2, col=1)

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Inflation Rate (United States)'],
                         mode='lines+markers',
                         marker=dict(color='green'),
                         name='Inflation Rate (United States)'),
              row=3, col=1)

fig.update_layout(height=800,
                  width=900,
                  showlegend=False,
                  title_text="Trends of Exchange Rate and Inflation Rates",
                  xaxis3_title="Year",
                  template='plotly_white')

fig.update_yaxes(title_text="Exchange Rate (INR/USD)", row=1, col=1)
fig.update_yaxes(title_text="Inflation Rate (%)", row=2, col=1)
fig.update_yaxes(title_text="Inflation Rate (%)", row=3, col=1)

fig.show()

The provided analysis suggests that the Indian Rupee has generally depreciated against the US Dollar over time. However, there have been periods of both rapid depreciation and relative stability.

India's inflation rate has been more volatile, with periods of high inflation followed by more stable periods. In contrast, the US has generally experienced lower and more stable inflation rates.

These findings align with the expected relationship between inflation and exchange rates: higher inflation in a country often leads to currency depreciation.

## Correlation analysis to explore the relationship between the inflation rates and the exchange rates

correlation_matrix = merged_df[['Exchange Rate (INR/USD)','Inflation Rate (India)','Inflation Rate (United States)']].corr()

correlation_matrix

## Correlation Analysis Results

Exchange Rate vs. Inflation Rate (India): A weak negative correlation of -0.34 was found. This suggests that as India's inflation rate increases, the Indian Rupee tends to depreciate against the US Dollar.   
Exchange Rate vs. Inflation Rate (United States): A weak positive correlation of 0.24 was observed. This indicates that higher inflation in the US might be associated with a depreciation of the USD against the INR, but the relationship is not strong.
Inflation Rate (India) vs. Inflation Rate (United States): A very weak negative correlation of -0.12 was found, suggesting that the inflation rates of the two countries do not move together significantly.

## Comparative analysis to highlight periods of significant divergence or convergence between the inflation rates and the exchange rates

fig = go.Figure()

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Exchange Rate (INR/USD)'],
                         mode='lines+markers',
                         name='Exchange Rate (INR/USD)',
                         line=dict(color='blue')))

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Inflation Rate (India)'],
                         mode='lines+markers',
                         name='Inflation Rate (India)',
                         line=dict(color='orange')))

fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Inflation Rate (United States)'],
                         mode='lines+markers',
                         name='Inflation Rate (United States)',
                         line=dict(color='green')))

fig.update_layout(title='Comparative Analysis: Exchange Rate vs Inflation Rates (India & US)',
                  xaxis_title='Year',
                  yaxis_title='Value',
                  legend_title_text='Indicators',
                  template='plotly_white',
                  height=600,
                  width=1000)

fig.show()

Inflation and Exchange Rate Relationship:

The analysis reveals a complex relationship between inflation and exchange rates for India and the US:

Early 2000s: While India experienced high inflation, the exchange rate remained relatively stable. This suggests that other factors, such as global economic trends or domestic policy decisions, might have played a more significant role in determining the exchange rate.
Late 2000s to Early 2010s: A more pronounced correlation between inflation and exchange rates emerged. As India's inflation rate increased, the INR weakened against the USD.
2015 Onwards: A divergence between the two is observed. The exchange rate continues to rise, despite relatively low inflation rates in both countries. This suggests that other factors like economic growth, monetary policies, and global trade dynamics are more influential in shaping the exchange rate.

## Analyzing Inflation based on the Purchasing Power Parity (PPP)

PPP Theory: It suggests that exchange rates between two countries should adjust to equalize the price of a basket of goods.
Analysis Plan:
Calculate the expected exchange rate based on PPP.
Compare the actual exchange rate with the PPP-based expected exchange rate.
PPP-Based Exchange Rate Calculation: The formula involves the initial exchange rate and the inflation rates of both countries.
By comparing the actual and expected exchange rates, we can assess whether PPP holds in the given context. This analysis will provide insights into the factors influencing exchange rate movements beyond inflation differentials.

## Expected Exchange Rate = Initial Exchange Rate × ( 1 + Inflation Rate in India / 1 + Inflation Rate in the US)

initial_exchange_rate = merged_df['Exchange Rate (INR/USD)'].iloc[0]

# calculate expected exchange rate based on PPP
merged_df['Expected Exchange Rate (PPP)'] = initial_exchange_rate * (
    (1 + merged_df['Inflation Rate (India)'] / 100) / (1 + merged_df['Inflation Rate (United States)'] / 100)
).cumprod()

fig = go.Figure()

# plot actual exchange rate
fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Exchange Rate (INR/USD)'],
                         mode='lines+markers',
                         name='Actual Exchange Rate (INR/USD)',
                         line=dict(color='blue')))

# plot PPP-based expected exchange rate
fig.add_trace(go.Scatter(x=merged_df['Year'],
                         y=merged_df['Expected Exchange Rate (PPP)'],
                         mode='lines+markers',
                         name='Expected Exchange Rate (PPP)',
                         line=dict(color='orange', dash='dash')))

fig.update_layout(title='Actual vs. Expected Exchange Rate (PPP)',
                  xaxis_title='Year',
                  yaxis_title='Exchange Rate (INR/USD)',
                  legend_title_text='Exchange Rates',
                  template='plotly_white',
                  height=600,
                  width=1000)

fig.show()

## Summary:

PPP Deviations: The actual exchange rate often deviates from the PPP-based expected exchange rate, suggesting that factors beyond inflation influence the exchange rate.
Inflation Impact: Higher inflation in India generally leads to a weaker INR, while lower inflation in the US strengthens the USD.
Other Factors: Factors like economic growth, interest rate differentials, and geopolitical events also play a significant role in determining the exchange rate.
In conclusion, while inflation is a key driver of exchange rate movements, it's not the sole determinant. A comprehensive analysis requires considering a broader range of economic and geopolitical factors.
