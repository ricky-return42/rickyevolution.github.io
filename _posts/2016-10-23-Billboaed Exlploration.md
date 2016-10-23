---
layout: post
title: How Billboard champions are made
---

The head() operation shows the top 5 entries of a data frame. Looking at the result, we can tell that each row represents an individual track. There are 83 columns.  All the entries are self-explanatory apart from the ones that have the format ‘x”n”.week’.  These columns represents the ranking of the track in the ‘n’th week. It is very important to note that this is a relative measure since the tracks entered the board at different dates. 

{% highlight python %}

import pandas as pd #Pandas is a very powerful module which handles data as a data frame
import numpy as np #Fast and convenient modules for numerical operrations
import scipy as sp #Similar function as numpy more statistics focus
import matplotlib.pyplot as plt #plotting tool for python
import seaborn as sns #A nicer looking plotting tool
%matplotlib inline

billboard = pd.read_csv('../assets/billboard.csv')
billboard.head()

{% endhighlight %}


![Billboard.csv head](http://res.cloudinary.com/dexpzle9i/image/upload/v1477219542/Screen_Shot_2016-10-23_at_11.44.27_zun5vw.png)

The head() operation shows the top 5 entries of a data frame. Looking at the result, we can tell that each row represents an individual track. There are 83 columns.  All the entries are self-explanatory apart from the ones that have the format ‘x”n”.week’.  These columns represents the ranking of the track in the ‘n’th week. It is very important to note that this is a relative measure since the tracks entered the board at different dates. It is usually very convenient to use the following operation to gain more insight from a data frame.

{% highlight python %}
billboard.info()
{% endhighlight %}

It will provide us with the information such as column name, non-null entries and data type. However for the datasets with larger amount of columns, it would return a very long list which is not desirable. A similar result was also observed with the .describe() operation. The .describe() operation returns a summary statistics for numerical columns

{% highlight python %}
billboard.describe()
{% endhighlight %}

Jupyter has trouble printing all the columns out at once. Therefore the dataframe was divided into groups of 10 and 1 group is printed at a time. The key things to look for here are any abnormal values in min, max where they should be integers bigger than 0 and less than 100.


{% highlight python %}
for i in np.linspace(0,80,9):
    print billboard.describe().iloc[:,i:i+10]
    print'-------------------------------'
{% endhighlight %}






