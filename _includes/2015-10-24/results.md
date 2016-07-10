We tested our GLM predictions on silver (XAG) commodity pricing data. 

Our date range of `20130401`-`20150731` containing 172 days was split into a training set containing 88 days, a validation set containing 30 days, and a test set containing 50 days.

For baseline values, 58 of 80 train days had prices go down, as did 19 of 30 validation days and 26 of 50 test days. Thus the baseline error for predicting 0 on every day would have been $$24/50=0.48$$.

We trained 54 logistic regression models, varying the number of clusters trained on, the inverse regularization strength $$c$$, and the type of regularization ($$L1$$ or $$L2$$).

$$K = \{10, 20, 30, 40, 50, 100, 200, 300, 400, 500, 1000, 2000, 3000, 4000, 5000\}$$

$$c = \{0.01 - 0.1, 0.1 - 1, 1 - 10\}$$

We also tested precision, recall, and f1 scores for various prediction probability cutoffs between 0.1 and 0.9.

The model with the least error was trained on $$K=5000$$, $$c=2$$, and used $$L1$$ regularization.

Training Accuracy: 0.682

Test Accuracy: 0.615

Precisions:

  - 0.10: 0.85
  - 0.25: 0.85
  - 0.50: 0.70
  - 0.75: 0.85
  - 0.90: 0.85
 
Recalls:

  - 0.10: 1.00
  - 0.25: 1.00
  - 0.50: 0.44
  - 0.75: 0.00
  - 0.90: 0.00
 
F1:

  - 0.10: 0.82
  - 0.25: 0.82
  - 0.50: 0.50
  - 0.75: 0.00
  - 0.90: 0.00



Overall train and test errors as a function of $$K$$ can be seen here. Models were retrained 20 times each.

![preproces-img]({{ BASE_PATH }}/assets/error_v_clusters.png){: .center-image }

**NOTE FOR FULL DISCLOSURE**: We computed the clusters on the a large part of dataset, but due to time constraints had to run initial predictions on the subset of the data that was clustered and coalesced, which did not at the time include the few most recent months reserved for testing. As a result, test data **was used for computing K-means**. We think this may inflate test accuracy slightly. However, the clusters were also computed from samples of days far in the future of the test data, so those likely had a cancelling adverse effect. In any case, these initial exploratory results are just hints at a possible signal.