# 9. Time Series in Python
### *112012068 Oskar Wladar*

## Series Data Structure Recap
### Importing Numpy and Pandas library
	import pandas as pd
	import numpy as np
### Creating Series from a list
	ser = pd.Series([1, 3])
	print(ser) 
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/261c5df5-9396-4303-a555-da94786ae626)

### Creating Series with String as index
    prices = {'apple': 4.99,
              'banana': 1.99,
              'orange': 3.99}
    ser = pd.Series(prices)
    print(ser)
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/980d0f28-0b58-4379-8e67-466d5cac4434)

## Example: Time Series Analysis of Google Trends  
Our goal is to get google trends data of keywords 'gym', 'diet' and 'finance' and analyze how they change over time  
Are there gonna be more searches of these words at the start of a new year?

### Let's start with reading the data we will work with
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
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/9e187f5f-5bfd-4e24-bb64-58b71dbd408a)

### Let's rename the columns to be easily readable
	# Renaming the columns
 	df.columns = ['month', 'diet', 'gym', 'finance']

  	# And let's print the result
   	print(df.describe())
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/14b4103e-ca4e-4fb1-9c86-733b856d7929)

### Now we need to convert the month column into DateTime data type so we can start doing some cool stuff
	df.month = pd.to_datetime(df.month)
 	df.set_index('month', inplace=True)
  	# Note that we use the inplace parameter to change the original indexing rather than creating new object

 	print(df.head())
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/195a6a99-e6c1-463f-9ad2-0cb8efcc7063)  
![image](https://github.com/TheGingeros/presentations/assets/81049688/d8d362a8-34f7-45a9-94f3-2ee424ebc2b1)

### So let's visualize our Time Series object
	df.plot()
	plt.xlabel('Year')
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/5dbc392e-c1b7-4399-8eb4-bcddda14d3a5)  
The Y axes represents the search interest relative to the highest point on the chart for the given region
and time. A value of 100 is the peak popularity for the term. A value of 50 means that the term
is half as popular. Likewise a score of 0 means the term was less than 1% as popular as the
peak.  

We manage to create a pretty cool graph, but is it easy to read? Let's use resampling, smoothing and rolling average to visualize the 'diet' data better.    

**Rolling Average:** For each time point, take the average of the points on either side of it (the number of points is effecte by the window size!)

### Let's start by extracting the 'diet' column from our time series object
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
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/e2ee52bf-3055-4c8d-931f-c775e844a436)

### We can even combine both 'diet' and 'gym' data by creating new DataFrame which will be concatenation of both 'diet' and 'gym' smoothed data
	# Extract the gym data
	gym = df['gym']

  	# Create the new DataFrame
	df_avg = pd.concat([diet.rolling(12).mean(), gym.rolling(12).mean()], axis=1)

 	# And plot it
	df_avg.plot()
	plt.xlabel('Year')
*Output:*  
![image](https://github.com/TheGingeros/presentations/assets/81049688/bfcac076-7e7b-4954-86d1-6ec094b954ad)


