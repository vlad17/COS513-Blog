
Previously, we used logistic regression to predict the price movements for silver. We ran our models on a training set containing 88 days, a validation set containing 30 days, and a test set containing 50 days.

The test accuracy as a function of $$K$$ for the best parameters can be seen here:

![preproces-img](/assets/error_v_clusters.png){: .center-image }

Since this is not very convincing that our model outperforms the benchmark, we expanded our date range to `20130401`-`20151001`. There were 526 days for training, 207 days for validation and 152 days for testing. Out of the 152 days, the prices increased in 58 days (61.8% of the days were decreasing).

We performed logistic regression with l1 and l2 regularization with different regularization weights in [0.01, 1000].

We also reduced the number of dimensions with PCA and performed SVM with error penalties in [0.01, 1000] with different kernels on the data.

We pick the parameters that produce the highest accuracy on the validation set and calculate the accuracy on the test dataset. The best models from both logistic regression and SVM only produced the same accuracy as the bestline on the test set (guessing that every day goes down).


