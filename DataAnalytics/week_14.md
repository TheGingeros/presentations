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

