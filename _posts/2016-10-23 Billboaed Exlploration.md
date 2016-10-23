---
layout: post
title: How Billboard champions are made
---

I apologise for the messy blog last week and I hope it can be done better!. Therefore in this week's blog there would be a mini guide to demonstrate how one can use Python and its very powerful modules to perform data analysis! The dataset that we will be using today is the Billboard Hot 100 data back in 2000.

{% highlight python %}

import pandas as pd
import numpy as np
import scipy as sp
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

billboard = pd.read_csv('../assets/billboard.csv')
billboard.head()

{% endhighlight %}


![SAT 2001 Info](http://res.cloudinary.com/dexpzle9i/image/upload/v1476624145/Screen_Shot_2016-10-16_at_14.21.06_g5y4f1.png)



