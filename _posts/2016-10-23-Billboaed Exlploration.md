---
layout: post
title: How Billboard champions are made
---

I apologise for the messy blog last week and I hope it can be done better! Therefore in this week's blog there would be a mini walk through to demonstrate how one can use Python and its very powerful modules to perform data analysis! The dataset that we will be using today is the Billboard Hot 100 data back in 2000. The Billboard chart is the standard for music industry in the United States. Let’s dive in and explore the data!

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

After the inspections it seems that all the contained values are reasonable. However giving the data a deepe thought, one might wonder whether all the tracks exists on the board continuously. Is it possible for a track to be dropped from the board and return? The following code returns a list of boolean values where a False indicates a track has made a come back.

{% highlight python %}
weeks = billboard.iloc[:,7:]
weeks['nncount'] = weeks.notnull().sum(axis=1)
continuous_lst = []
for i in range(weeks.shape[0]):
    ind = weeks.nncount[i]
    continuous_lst.append(all(weeks.iloc[i,:ind-1].notnull())) #end of list is ind-1 because we have introduced column 'nncount'
print continuous_lst
{% endhighlight %}

It is found out that there were 9 tracks that successfully made a come back!

Here I am going to introduce another operation that is very useful for summary analysis. It is the .value_counts() operation. This operation can be seen as a pivot table showing the counts for each unique value in the series. The following are examples where we can use .value_ counts()!

{% highlight python %}
billboard['track'].value_counts() #To find out whether track names are unique
billboard['genre'].value_counts() #To see how are the music genre distributed
billboard['artist.inverted'].value_counts() #To see how many entries per artist
{% endhighlight %}

Here is a brief summary of the data set:

After the investigation, we can see that there are 317 rows and 83 columns in the data. Out of them there are 6 columns with a data type 'object' (Which would very likely be strings).  It is important to note that the date columns namely 'date entered' and 'date peaked' can possibly be transformed to datetime values for further analysis. The 'time' column can also be transformed into integer to investigate the relationship between the length of a track and its popularity. It is also worthwhile to note that there are two tracks both named "Where I Wanna Be" and it is important not to mix them up. There are a total of 10 genres of music and Rock is the most popular. There are 228 unique artist/groups involved in the dataset. Jay-z has the most track entries of 5. From week 66 onwards all the columns are empty which means no tracks has stayed in the chart for more than 65 weeks. Therefore the extra columns should be removed. The track that stayed the longest is Higher performed by Creed. Finally it is very easy to be tricked by the results returned by .info() operation. This is because it is shown that the number of non-null values decreases with the week which is very normal. However under a more detailed investigation it appears that those numbers were not composed of the same group of tracks everytime. For example track 'a', 'b', 'c' are both in the board at week 1 (count = 3). Track 'a' dropped out in week 2 (count =2). In the third week track a comes back but track 'b' and 'c' drops out (count =1). This behaviour can potentially complicates the analysis and catch out the less careful analyst.



