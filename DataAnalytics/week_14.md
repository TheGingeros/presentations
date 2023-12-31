# 9. Time Series in Python
### *112012068 Oskar Wladar*
### *工管三乙 109A50009 楊儀珈*


## Series Data Structure Recap
### Importing Numpy and Pandas library
```py
import pandas as pd
import numpy as np
```
### Creating Series from a list
```py
ser = pd.Series([1, 3])
print(ser)
``` 
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/261c5df5-9396-4303-a555-da94786ae626)

### Creating Series with String as index
```py
prices = 
{
'apple': 4.99,
'banana': 1.99,
'orange': 3.99
}
ser = pd.Series(prices)
print(ser)
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/980d0f28-0b58-4379-8e67-466d5cac4434)

## Example: Time Series Analysis of Google Trends  
Our goal is to get google trends data of keywords 'gym', 'diet' and 'finance' and analyze how they change over time  
Are there gonna be more searches of these words at the start of a new year?

### Let's start with reading the data we will work with
```py
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
%mathplotlib inline

# Save the url with our data into variable
url = "https://raw.githubusercontent.com/datacamp/datacamp_facebook_live_ny_resolution/master/datasets/multiTimeline.csv"

# Read the .csv table
df = pd.read_csv(url, skiprows=2)

# Let's see how the data looks
print(df.head())
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/9e187f5f-5bfd-4e24-bb64-58b71dbd408a)

### Let's rename the columns to be easily readable
```py
# Renaming the columns
df.columns = ['month', 'diet', 'gym', 'finance']

# And let's print the result
print(df.describe())
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/14b4103e-ca4e-4fb1-9c86-733b856d7929)

### Now we need to convert the month column into DateTime data type so we can start doing some cool stuff
```py
df.month = pd.to_datetime(df.month)
df.set_index('month', inplace=True)
# Note that we use the inplace parameter to change the original indexing rather than creating new object

print(df.head())
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/195a6a99-e6c1-463f-9ad2-0cb8efcc7063)  
![image](https://github.com/TheGingeros/presentations/assets/81049688/d8d362a8-34f7-45a9-94f3-2ee424ebc2b1)

### So let's visualize our Time Series object
```py
df.plot()
plt.xlabel('Year')
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/5dbc392e-c1b7-4399-8eb4-bcddda14d3a5)  
The Y axes represents the search interest relative to the highest point on the chart for the given region
and time. A value of 100 is the peak popularity for the term. A value of 50 means that the term
is half as popular. Likewise a score of 0 means the term was less than 1% as popular as the
peak.  

We manage to create a pretty cool graph, but is it easy to read? Let's use resampling, smoothing and rolling average to visualize the 'diet' data better.    

**Rolling Average:** For each time point, take the average of the points on either side of it (the number of points is effecte by the window size!)

### Let's start by extracting the 'diet' column from our time series object
```py
diet = df['diet']

# Let's resample the data using the .resample() method with parameter 'A' = year frequency
diet_resamp_yr = diet.resample('A').mean()

# And apply the rolling average - 12 means that the size of the window will be 12 (12 time points in the graph)
diet_roll_yr = diet.rolling(12).mean()

# And let's plot this bad boy with some simple legend
ax = diet.plot(alpha=0.5, style='-') # store axis (ax) for latter plots
diet_resamp_yr.plot(style=':', label='Resample at year frequency', ax=ax)
diet_roll_yr.plot(style='--', label='Rolling average (smooth), window size=12', ax=ax)
ax.legend()
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/e2ee52bf-3055-4c8d-931f-c775e844a436)

### We can even combine both 'diet' and 'gym' data by creating new DataFrame which will be concatenation of both 'diet' and 'gym' smoothed data
```py
# Extract the gym data
gym = df['gym']

# Create the new DataFrame
df_avg = pd.concat([diet.rolling(12).mean(), gym.rolling(12).mean()], axis=1)

# And plot it
df_avg.plot()
plt.xlabel('Year')
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/bfcac076-7e7b-4954-86d1-6ec094b954ad)

### First order Differences in the data
```py
# Use the .diff() method to calculate differences between each month
df.diff().plot()
# And plot it
plt.xlabel('Year')
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/985c5879-d93a-48db-87be-25a7c08d284d)

### Correlation  
* Describes the degree to which two variables change together
* Example:
	* Positive Correlation: An increase in one variable is associated with an increase in the other
	* Negative Correlation: An increase in one variable is associated with a decrease in the other
* We use **Correlation Coefficient** to measure correlation, denoted by *r*, which ranges from -1 to 1
 
```py
# First let's plot the original data to see what we are working with
df.plot()
plt.xlabel('Year');
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/42055a19-031b-49be-af9a-e160aa289f2f)  

```py
# Let's print the correlation for this and its matrix
print(df.corr()) 
sns.heatmap(df.corr(), cmap="coolwarm")
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/dbb3dfe8-70ab-4952-b655-9a0f99a8c9c0)  
![image](https://github.com/TheGingeros/presentations/assets/81049688/f7cbcfa1-2e00-4d96-bac3-3d49e141d4ac)  

We can see that the both 'diet' and 'gym' are negatively correlated.  
Correlation consists of two components:   
* **Trend components** - negatively correlated
* **Seasonal components** - positively correlated  
  
The actual correlation coefficient is actually capturing both of those. So let's find!

### Calculation of seasonal correlation
* correlation of the first-order differences of these time series

#### Let's plot the first order differences first
```py
df.diff().plot()
plt.xlabel('Year');
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/20c9290d-eb9b-4189-af02-e2379d08dc9c)  

#### Now let's look at the correlation of these differences in the matrix
```py
print(df.diff().corr())
sns.heatmap(df.diff().corr(), cmap="coolwarm")
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/5731a727-366a-452b-bfe9-dbd1e4dad850)  
![image](https://github.com/TheGingeros/presentations/assets/81049688/2025ac76-3d4a-4152-ab45-b82db6d9bf1d)

#### Now we need to decompose these into trend, seasonality and residuals
* **Trend component** - represents the long-term, overall direction in which the time series is moving
* **Seasonal component** - accounts for regular, repeating patterns or fluctuations that occur at fixed intervals within a specific time frame
* **Residuals** - errors/noise, capture the unexplained or irregular patterns that are not accounted for by the trend and seasonality  

#### Let's dive into it
```py
# Start by importing season_d
from statsmodels.tsa.seasonal import seasonal_decompose

# Brute force float type (they did it in the textbook idk)
x = gym.astype(float) # force float

# Use the season_decompose method to store the decomposition
decomposition = seasonal_decompose(x)

# Extract the trend component
trend = decomposition.trend

# Extract the seasonal component
seasonal = decomposition.seasonal

# And the residual component
residual = decomposition.resid

# And let's do plot magic to see all of this
plt.subplot(411)
plt.plot(x, label='Original')
plt.legend(loc='best')
plt.subplot(412)
plt.plot(trend, label='Trend')
plt.legend(loc='best')
plt.subplot(413)
plt.plot(seasonal,label='Seasonality')
plt.legend(loc='best')
plt.subplot(414)
plt.plot(residual, label='Residuals')
plt.legend(loc='best')
plt.tight_layout()
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/bdbb9e61-a3dc-4f6e-b16e-ddba2b63ba74)

### Autocorrelation
* **Autocorrelation Function (ACF):**
	* measure of the correlation between the TS with a lagged version of itself. For instance at lag 5, ACF would compare series at time instant t1. . . t2 with series at instant t1-5. . . t2-5 (t1-5 and t2 being end points).

```py
# Import the method we need
from pandas.plotting import autocorrelation_plot

# Force the float type
x = df["diet"].astype(float)

# And plot
autocorrelation_plot(x)
```
 *Output:*  
 ![image](https://github.com/TheGingeros/presentations/assets/81049688/68c9529b-d162-4a5e-b155-ae4755dd71f9)
 #### And let's compute the ACF
```py
# import the pre-defined function
from statsmodels.tsa.stattools import acf

# first item is NA
x_diff = x.diff().dropna()

# Define the lag
lag_acf = acf(x_diff, nlags=36)

# and plot it
plt.plot(lag_acf)
plt.title('Autocorrelation Function')
```
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/6594e504-4776-4925-b8e6-7af14f61e3e0)  
ACF peaks every 12 months: Time series is correlated with itself shifted by 12 months.




