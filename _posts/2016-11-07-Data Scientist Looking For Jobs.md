---
layout: post
title: Data Scientist Looking For Jobs
---

## Introduction

In the recent years the field of data science has undergone a rapid growth. The society has recognised the importance of data and the insight it provides. According to an <a href='https://hbr.org/2012/10/data-scientist-the-sexiest-job-of-the-21st-century'>article</a> written by the Havard Business Review, data scientist would be one of the most popular jobs in the 21st century. It is expected that a large number of candidates would like to enter the field each year to fill the demand.

It would be very desirable to allow candidates to understand what salary to expect when they are applying for jobs. The increase in transparency would benefit both the employers and employess to reach an agreement with a balance of salary and skillset trade offs.

Job seeking websites are popular places for candidates to look for jobs now a days. However the sheer amount of data would make analysis difficult by just eyeballing the results. Hence this project aims to utilise machine learning method to categorise data scientist jobs into different salary bins. The target website is indeed.com which is popular around the globe. Logistic regression will be used to divide the samples into 3 paying bings namely high, mid and low. This would provide a general idea to candidates without being too specific. Specificity can introduce lots of errors as afterall job application procedures involve lots of human interactions. This would cause lots of unpredictabilitu and instability. Therefore it is decided to treat this problem as a classification rather than regression. 

#### Goal : To successfully categorise a job as high or low paying by analysing the details of the advert in indeed.com

## Scrape the web

An algorithm is written to automaticall scrape data from indeed.com. From sources origin from indeed.com and a famous data science website called KDNuggets, it is decided that the job adverts from of 13 cities will be collected (see Appendix). The scraped data includes the information as described below:
- Job title
- Company name
- Location
- Salary if avialble
- Skills mentioned in the exact advert

The former 4 items can be obtained directly from indeed.com and the later needs to be scrapped from the exact job advert. This provides a challenge as the scrapping algorithm has difficulties in adapting to different webpage formats. This however can be solved by pre-defining a list of popular skills and check if there is a match in the page. Although this method might not be the most accurate, it is the easiest to implement and effective enough for this first exploration.

Another problem encountered is that the response from indeed.com can produce random error (this error would return the 1st page of results no matter what we enter as input). Yet this problem is solved by maintaining a list of pages to scrape and keep working until all data is obtained.

The resulting data is over 5000 instances. However we are only interested in instences which have salary information so this significanlt reduce the number to just above 300.

The data are posted by the recruiting companies themselves so in this analysis we would assume them to be correct and without error. It is alos asusmed that the data are posted recently so we are obtaining updated results.assets/billboard.csv')
billboard.head()

## Data Cleaning and exploratory analysis

The obtained data has several formatting issues and missing values. They have to be dealt with in order for the analysis and prediction to be effective. Some extra columns are introduced as a result of feature engineering in order to try maximising the accuracy of the model. The most important cleaning is to transform the salary data into yearly estimates. This is because some salary data are presented in values per month/week/day/hour hence the transformation is necessary for fair comparison. Result looks something like this:

![dfhead](http://res.cloudinary.com/dexpzle9i/image/upload/v1478564135/Screen_Shot_2016-11-08_at_00.14.59_eaim3w.png)

The target variable of whether a job is high paid is also generated This is done by splitting the dataset into 2 categories using the median salary value. Median is used as central tendancy measurement because the distribution in salaries are positively skewed with little very high paying jobs (in relative terms).

![DS salary distribution](http://res.cloudinary.com/dexpzle9i/image/upload/v1478563835/Screen_Shot_2016-11-08_at_00.09.31_jp1yrr.png)

## Model fitting and results

It is decided that this problem should be treated as a classification problem because job advertising is a process with significant human intervention which introduce lots of error. Therefore an exact estimate using regression model is not the most appropriate. The initial model is fitted with only the locaion data and it generates a result of 78% However the location data has many specific values which can easily lead to overfitting (capturing noise rather than the trend). Therefore a more generalised attributes is used whihc only describes the city/town. This reduced the accuracy to 76%. Seeing the opportunity of increasing accuracy, more variables are introduced to the model. These variables takes into account of the job title, skills and payment rate. These are all categorical variables so dummies were generated. Combined with the area data they produce an accuracy of 88.6%

Since we predict and test using the same data we are in great danger of making a model that overfits. Hence a 8 fold cross validation is used to futher reduce uncertainty in predicting new data. The average accuracy is reduced to 72% but we can be certain that the model would provide stable results. Then we move on to tune the parameters by altering regularisations. After repeatedly trying different combinations (using grid search), the best accuracy result suggested is 75%. 

There are 2 important values in the evaluation of classification analysis and they are precision and recall.

Precision is calculated by: $$\frac{True Positive}{True Positive + False Positive}$$

and Recall is calculated by: $$\frac{True Positive}{True Positive + False Negative}$$

In our case precision is the probability that a job is high paid when we predict them to be high paid. Recall on the other hand represents how likely it is for us to predict high paying jobs as they are. Although in the first place they might sound similar, there are large implications behind these numbers. For example in medical diagnosis we would like a very high recall because we do not want a lot of false negative (which could be lethal). On the other hand if we were an intelligence officer during the cold war times, precision is critical. This is because we do not to mis-interpret a false positive missile attack and launch our own nuclear weapon (hence initiating a war, and/or the destruction of mankind...). However these two terms are trade-offs and we can not improve one without sacrifising the other (when we use the same model). Regarding to our problem, we do not wish to tell the client that he will receive high salary whereas in reality this is not true. Therefore we would like to maxmise precision by sacrifising recall. The result confusion matrix is shown here.

![confusion matrix](http://res.cloudinary.com/dexpzle9i/image/upload/v1478564475/Screen_Shot_2016-11-08_at_00.20.53_eapual.png)


We understand that depending on the problem, precision and recall scores can help us to adjust how the algorithm behaves in order to give the best predictions. Another way to visulise this problem is through a metric call AUROC plot. 

![ROC](http://res.cloudinary.com/dexpzle9i/image/upload/v1478564563/Screen_Shot_2016-11-08_at_00.22.24_osnvi6.png)

From the plot above we can see that as recall increases so does false positive rate which means the reduction in precision.

After the model was created and fitted, the coefficients are extracted to see what keywords are more correlated to job salaries. It is found out that words such as 'engineer/ engineering' and 'quantitative' are positively related whilts 'technicain', 'monthly' and 'Oakland' are negatively related.

## Conclusion and future work

From the work carried out it can be safely said that one can predict salaries using the data obtained from adverts. Our stable cross validated result suggests a good accuracy (75%) in comparison to pure guessing (50%). The model is also tuned to maximise precision in order to avoid giving false hopes to client.

The project goal is achieved where we built a successful predictive model. However it is suggested that more results have to be obtained from the websites to improve the model. This is because considering all the job adverts in the internet, 300 samples are really small and the results are most likely not representative. The scrapping algorithm can aslo be improved to cure the problem of repetition mining which wastes lots of time.



