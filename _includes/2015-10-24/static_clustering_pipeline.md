
#### Key Points Driving the Pipeline

Let us recall the key issues we want to address in our new pipeline, detailed in the last post:

* Pull out richer GDELT info 
* Cluster on news-topic-related information (this is really the core of our hypothesis of prediction by GDELT).
* Represent non-orthogonal column relationships
* Manage high volumes of data: use strategies to increase parallelism and minimize the serial bottlenecks without sacrificing too much accuracy.

#### High-level structure

The data has several stages (numbers below are accurate to within the order of magnitude). Data is stored in a `TSV` `ASCII` format.

**Preprocess** refers to extracting and sanitizing relevant GDELT features. 

**Expanding** refers to transforming the preprocessed data into wide numerical feature vectors. 

**Clustering** refers to classifying expanded data according to a $$K$$-means model. This model is computed beforehand from a static random sample of our data.

**Conglomeration** coalesces a day's data, in a manner that will be described shortly.

We parallelized usually to the maximum possible on `cycles.cs.princeton.edu`: `48`. Parallelizing along the following for-loops:

To learn the $$K$$-means, we first perform for each day:

1. Random sample each day with equal number of rows
2. Preprocess
3. Expand

Then for each $$K$$, learn a mini-batch-based $$K$$-means on the concatenated output from (3), above.

For the actual regression, for each day, we:

1. Preprocess
2. Expand
3. Cluster and Conglomerate

Finally, for each set of GLM hyper paramters, we learn and validate on the concatenated output of (3) from above.

#### Preprocessing stage

_Raw data_: 58 columns, mixed types

_Preprocessed data_: 19 columns, numeric, categorical, or string (coming soon!). Categorical values are represented as integers in $$[1, N]$$, with 0 representing an empty cell. When expanded to the one-hot encoding, 0 doesn't get its own value (it'd be wrong to learn off missing data - **or is it?**).

![preproces-img]({{ BASE_PATH }}/assets/preprocess.png){: .center-image }

#### Expansion stage

_Expanded data_: 945 columns, all numeric.

![expand-img]({{ BASE_PATH }}/assets/expand.png){: .center-image }

### Cluster stage

_Coalesced data_: 1 row, $$(K+1)(I+1)$$ columns, where $$I$$ is the number of "importance-related" columns (note minor typo in last stage below).

![cluster-img]({{ BASE_PATH }}/assets/cluster_and_transform.png){: .center-image }

_Label data_: Time series of prices. We generate day-to-day diffs from this. It's small enough to fit in memory and generate on the fly.

**Importantly**, we divide each day's summary vector (the result of the matrix multiply and subsequent flatten) by the number of rows $$N$$ for that day, so days aren't skewed by event counts (consider the final constant column).

#### Generalized Linear Model

Currently a logistic classifier. Hyper parameters are regression ($$L_1,L_2$$), $$K$$, and regularization coefficient.

#### Data Used

Clusters learned from random sample of `20130401`-`20150731`, `150` lines per day. `200MB`, `120K` lines total. Each clustering model (for different $$K$$) takes between `1-50MB`.

Raw data `20130401`-`20151021` (a few extra months to test on data that didn't affect the random sample for clustering). `60GB` total. Coalesced day-summaries (acutally used for regressions) take up `5-200MB`, depending on $$K$$.
