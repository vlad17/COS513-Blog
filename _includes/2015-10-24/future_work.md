#### Multicomputer parallelism

The above pipeline, run on one machine with 48 CPUs, handled $$\frac{3}{4}$$ of a year's worth of data in 8 hours (as of the writing of this post, it's still going).

As we change our feature extraction methods from the raw data (_see below_), iterative updates to the day-summaries need to be expedited.

First priority is to get the sampling and clustering pipelines (mainly the latter) running on a beowulf cluster, `ionic.cs.princeton.edu`, with about 500 accessible CPUs. This will get us down to about $$1$$ hour per year of data - still not too great, we could go up to about 900, at one CPU per day.

#### Word2Vec

Word2Vec is an unsupervised learning algorithm that finds a mapping from words or phrases to $$\mathbb{R}^D$$ for a set parameter $$D$$. This is done by initializing randomly, and then updating tokens' word vectors (words or phrases, as identified by a frequency-based bigram/trigram/quadgram model) with linear combinations of surrounding tokens' current word vectors according to the corpus.

We initially tried Brown's NLTK corpus with quadgrams. Unfortunately, common actor names, like 'singapore' or 'protester' were not present in the corpus.

After we expand the corpus to a larger (still _static_) one, we can utilize some of GDELT's string column features by expanding them to $$\mathbb{R}^D$$ in the expand phase.

#### Normalization and scale

Currently, the topic-columns are not scaled to their z-scores (the per-day parallelism would break down if we computed the data-set-wide $$\mu,\sigma$$). This negatively impacts clustering because high-magnitude columns dominate distance.

**Possible solution**: A preferable alternative is to rely on the manageable size of the random sample we learn the clusters from. Prior to fitting K-Means, we can compute $$\hat{\mu},\hat{\sigma}$$ from the sample itself. Then we compute the z-scores for each column.

**Question**: do we need to be careful to use an [unbiased estimator](https://en.wikipedia.org/wiki/Unbiased_estimation_of_standard_deviation) for $$\sigma$$? This isn't a true random sample anyway, would the correction even make sense? The point is just to scale the values to reasonable ranges.

#### Grab bag of other stuff to try out later:

1. Smarter clustering: Don't create a sparse binary matrix of clustered news, create a matrix of posteriors for a news-GMM.
2. Nonparametric clustering: Infinite GMM, HDP, SN&Gamma;P
3. Other classifiers: SVM
4. Linear regression on $$\frac{p_{t+1}-p_t}{p_t}$$.
5. Try other commodities, try older data.

The most up-to-date TODO list is in the README in the [repo](https://github.com/vlad17/COS513-Finance).