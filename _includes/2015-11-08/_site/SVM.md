
Previously, we used logistic regression to predict the price movements for silver. We ran our models on a training set containing 88 days, a validation set containing 30 days, and a test set containing 50 days.

This time, we expanded our date range to `20130401`-`20151001`. There were 526 days for training, 207 days for validation and 152 days for testing. Out of the 152 days, the prices increased in 58 days (61.8% of the days were decreasing).

The best result from logistic regression and SVM were both 61.8%.


python svm.py 2013-04-01 2014-09-30 2014-10-01 2015-04-30 2015-05-01 2015-10-01 100 XAG


