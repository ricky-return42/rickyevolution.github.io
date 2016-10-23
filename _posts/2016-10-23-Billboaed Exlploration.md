---
layout: post
title: How Billboard champions are made
---

I apologise for the messy blog last week and I hope it can be done better! Therefore in this week's blog there would be a mini walk through to demonstrate how one can use Python and its very powerful modules to perform data analysis! The dataset that we will be using today is the Billboard Hot 100 data back in 2000. The Billboard chart is the standard for music industry in the United States. Let’s dive in and explore the data!

<h2>Step 1: Load the data and do some preliminary look around!</h2>

{% highlight python %}

import pandas as pd #Pandas is a very powerful module which handles data as a data frame
import numpy as np #Fast and convenient modules for numerical operrations
import scipy as sp #Similar function as numpy more statistics focus
import matplotlib.pyplot as plt #plotting tool for python
import seaborn as sns #A nicer looking plotting tool
from datetime import timedelta #This moduel deals with time data
%matplotlib inline

billboard = pd.read_csv('../assets/billboard.csv')
billboard.head()

{% endhighlight %}


![Billboard.csv head](http://res.cloudinary.com/dexpzle9i/image/upload/v1477219542/Screen_Shot_2016-10-23_at_11.44.27_zun5vw.png)

We start off by importing all the modules that will be (could be) helpful during the analysis. Then the targeted csv file is read in. The head() operation shows the top 5 entries of a data frame. Looking at the result, we can tell that each row represents an individual track. There are 83 columns.  All the entries are self-explanatory apart from the ones that have the format ‘x”n”.week’.  These columns represents the ranking of the track in the ‘n’th week. It is very important to note that this is a relative measure since the tracks entered the board at different dates. It is usually very convenient to use the following operation to gain more insight from a data frame.

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

<h2>Step 2: Clean the data and turn it into something more useful!</h2>

Although the data seems interesting, it must first be cleaned before using! Cleaning a data set speeds up the analysis process and lowers the chances of error occuring. It is mentioned that in our dataset there are quite a few empty columns. Therefore it would be nice to get rid of them

{% highlight python %}
billboard = billboard.iloc[:,:72] #get rid of the empty columns by slicing
{% endhighlight %}

It is also important to note that some columns have names that are either unnecessarily long or consists of a '.' character. The former makes the column difficult to undersntad/read and the later might cause Python to exhibit unpredictable behaviour due to the dot notation. Therefore it is also a good practise to rename colums to as simple as possible. If spaces are need between words, an underscore '_' character can be used.

{% highlight python %}
billboard = billboard.rename(columns={'artist.inverted':'artist', 
				  'date.entered':'date_entered',
				  'date.peaked':'date_peaked'})
{% endhighlight %}

As mentioned in step 1, we would like to look at the relationship between a track's length and its other properties. However the track length is currently in a format of mm:ss as a string. It is not very easy to use so a function is written to convert that into number of seconds as integers. The time column would not be very useful either after the conversion so it will be dropped. The same case also applies for 'date_entered' and 'date_peaked'. Therefore their data types are transformed from strings into datetime which allows us to measure date difference as timedeltas. It would also be nice to create an extra column where we store the month of 'date_entered'. This would allow us to conveniently investigate any cyclic behaviour that might exsist.

{% highlight python %}
def time_con(str):
    lst = str.split(':')
    secs = int(lst[0])*60 + int(lst[1])
    return secs
billboard['time_in_seconds'] = billboard.time.apply(time_con)
billboard.drop(['time'], axis =1, inplace = True)
billboard['datetime_entered'] = pd.to_datetime(billboard['date_entered'])
billboard['datetime_peaked'] = pd.to_datetime(billboard['date_peaked'])
billboard.drop(['date_entered','date_peaked'], axis =1, inplace = True)
billboard['month_entered'] = billboard['datetime_entered'].apply(lambda x:x.month)
{% endhighlight %}

The huge number of columns in the original dataframe is not optimal for some data analysis such as time series.Using the .melt() function in Pandas, one can pivot the weekly ranking data into rows. This operation changes the 65 non-empty ranking columns into 2 columns namely 'week' and 'rank'. There will now be multiple entries for each track, one for each week on the Billboard rankings. The following code describes the operations that prepare and perform the melt.

{% highlight python %}
# Preperation
def re_name(x):
    if (len(x)==10) & (x[0]=='x'):
        return int(x[1:3])
    elif (x[0]=='x'):
        return int(x[1])
    else:
        return x
billboard.rename(columns=lambda x: re_name(x), inplace=True)

#Actual melt and clean up
melt_value_list = list(billboard.columns)[4:-5] #The columns to transform
melt_index_list = list(billboard.columns)[:4] + list(billboard.columns)[-5:] #The columns to keep
billboard_melted = pd.melt(billboard, id_vars = melt_index_list, value_vars= melt_value_list)
billboard_melted.rename(columns = {'variable':'week', 'value':'rank'}, inplace=True) #Rename columns
billboard_melted = billboard_melted[billboard_melted['rank'].notnull()] #Remove redundant columns
# Create a column to show the current week
dt = timedelta(days=7)
billboard_melted['week'] = billboard_melted['week'].astype(int)
billboard_melted['datetime_current'] = billboard_melted['datetime_entered'] + (billboard_melted['week']-1)*dt
billboard_melted['week'] = billboard_melted['week'].astype(int)
billboard_melted['genre'] = billboard_melted['genre'].astype('category')
{% endhighlight %}

We now have 2 data frames. It would be nice to keep the original dataframe too as different dataframes are better suited at different type of analysis. 