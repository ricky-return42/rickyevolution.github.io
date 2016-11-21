---
layout: post
title: Learning From Disaster: Part 0
---

## Summary

#### Introduction, problem and goal

This project is to help a client to identify factors that drives a movie's rating in IMDB. The client is a movie provide and would like to include high rating movies in their collection of products. The goal of the project is to obtain data for rated movie and successfully classifying them as high scorer or not. In order to be successful, we must define a baseline accuracy and try to out perform this benchmark.

#### Obtaining the data

A module called IMDB pie is used to obtain movie data from IMDB. The module works as a wrapper for the IMDB api which makes it easier to use. The data for top 250 movies in the IMDB is collected and stored into a CSV for later use.

#### EDA and data cleaning

From EDA it is found out that: 
- Most movies on the list has less than 200000 votes
- The median rating is 8.3
- The mdeian runtime is 7500s
- All the above mentioned data has a positive skew
- There are more modern movies than older movies on the list

Then some varaibles are transformed and analysed. For example trailers are transformed from URLs to counts of trailers as we cannot quantify the contents in the trailer. Release date has also been transformed to find out the month of release. It is found out that more movies on the list were released in winter months than summer months.

Other variables such as genre, cast, directors and plots are all cleaned and stored in seperate data frames to be analysed.

Some plots produced during the EDA is shown below.

![rating](http://res.cloudinary.com/dexpzle9i/image/upload/v1479769527/Screen_Shot_2016-11-21_at_23.04.02_flef2a.png)

![votes](http://res.cloudinary.com/dexpzle9i/image/upload/v1479769737/Screen_Shot_2016-11-21_at_23.08.36_vjfpyq.png)

![rating vs votes](http://res.cloudinary.com/dexpzle9i/image/upload/v1479769527/Screen_Shot_2016-11-21_at_23.03.13_dwonw5.png)

![years](http://res.cloudinary.com/dexpzle9i/image/upload/v1479769527/Screen_Shot_2016-11-21_at_23.04.17_lseth2.png)

#### Models

It is required that a tree based model to be used. Therefore to compare the tree based models with others, the models listed below were used:
- Decision Tree Classifier
- Random Forest Classifier
- Extra Trees Classifier
- Adaboost Classifier
- Gradient Boosting Classifier
- Logistic Regression Classifier
- K Nearest Neighbor Classifier
- Support Vector Machine Classifier
From inital fitting it is found out that the ensameble methods generally performs better than the normal methods except for extra trees. Logistic regression being a simple and non tree based model performs relatively well too. The model mentioned can achieve a test score (origined from a train test split of test size = 0.2) of over 80%. However the performance of single decision tree and SVM are rather poor where they barely meet or excced base accuracy. Cross validation was then applied to check for score consistency where most of the accuracy drops back to under 80%

Then features are scaled to see if results can be improved (especially for kNN and SVM where their classifying method rely heavily on spaces between points). However surprisingly all models performed worse. Normalising especially poor results for all models. Standardising also slightly reduced the accuracy models but improved the performace of SVM slightly. Again kNN surprisingly performed worse after features scaling.

Then we moved on to feature selection and hyperparameters tuning. For logistic regression, the method of recursive features elimination was applied with grid search for optimal hyper parameter. It is found out that all but 'Number of votes' and 'tag line existence' were wiped out during the feature selecting process or by the 'l1' regularisation. However this improved the accuracy back to the standard before the features were scaled.

For tree based ensamble models the number of estimators, max features and minimum sample splits were tuned. Most ensamble methods prefer a high number of esimators (1000 in this case). This is probably due to the increase in number of trees to average results in the increase in overall accuracy. 6 or more is the optimum max features. For minimum sample splits it is found out that 7 or more works best. All this however can not significantly improve the test accuracy. They can only match the accuracy when they were fitted with the raw data. However their cross validation score has improved to over 80% which suggest the model is more stable.

After that features such as cast, directors, certification, genre and plots were processed and fitted using either count verctorizer or TFIDF vectorizer. However none of them provide good information in suggesting whether a movie would be rated highly.

#### Conclusion and future work

An cross validated accuracy of >80%  demonstrates that we are capable of predicting the rating group of a movie (despite beating the baseline accuracy by less than 10%). The relevant coefficients shows that tag lines and number of votes are the only indicator of movie ratings.  Although the goal is achieved the project can be futher improved by obtaining more data. This includes both instances and features for example Oscar nominations and awards.