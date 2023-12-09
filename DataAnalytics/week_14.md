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



