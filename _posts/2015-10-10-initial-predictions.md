---
layout: post
category : class-posts
tagline:
tags : [exploratory-analysis,data,methods,dimensionality-reduction,logit,hmm,leveldb,t-sne]
---
{% include JB/setup %}

# Week 3: Initial Predictions

## Dimensionality Reduction Process

#### t-SNE Review

Approximation algorithm only relies on distance vectors, has linearithmic time and linear space requirements. Only downside is that it's a black box, so we can't update our dimensionality reduction based on the results of our predictions.

To give a brief glance into the paper (see last post for the link), here's the definition of the "similarity distribution" in high-dimensional space.

![from, pij]({{ stie.url }}/assets/hidim-sim-exact.png){: .center-image }

As you can expect, the KL divergence, which we're trying to optimize, will have a quadratic number of terms since there's $$O(N^2)$$ such $$p_{ij}$$.

Thus, for faster computation, we need a smaller approximate representation, which relies on neighborhoods constructed from nearest neighbors $$\mathcal{N}_i$$.

![from, pij approx]({{ stie.url }}/assets/hidim-sim-approx.png){: .center-image }

Note for completeness the low-dimensional similarity is given by:

![to, qij exact]({{ stie.url }}/assets/lodim-sim-exact.png){: .center-image }

This is the exact form of the low-dimensional Student-t distribution. We never really compute it explicitly, since we don't actually care what the low-dimensionality similarity distribution $$Q$$ is, but rather only are concerned with the representation of the KL divergence - or, more precisely, its gradient, which we are optimizing with the projected values $$\textbf{y}_i$$.

The quadratic term in the gradient is interestingly similar enough to an $$n$$-body simulation problem that we can use the [Barnes-Hut approximation problem](https://en.wikipedia.org/wiki/Barnes%E2%80%93Hut_simulation) to approximate it.

Note the above step implies the use of a $$2^d$$-tree, where $$d$$ is the dimension we're reducing to, so there's a serious cap on $$d$$.

#### t-SNE Results

**MNIST**

Double-encoded MNIST (from paper, had PCA run initially to 50 dims for speed):

![t-sne mnist]({{ stie.url }}/assets/tsne_map.png){: .center-image }

Byte-encoded MNIST (t-SNE only in [my fork](https://github.com/vlad17/bhtsne/tree/mnist)), `10000 imgs x (28x28) pixels` trains in 164s:

![t-sne out]({{ stie.url }}/assets/mnist-color.png){: .center-image }

**GDELT**

News data reduction. 2 days' worth of data - `326604 x 14` data matrix, with categories. Took <2 hrs. Just two instantiations for parameters, perplexity $$u = 30$$ for the high-dimensional distribution and cutoff $$\theta = 0.5$$ were used. Note the higher default perplexity causes the typical set to be larger, which in turn results in high standard deviations in the high-dimensional distribution. When encoded to the low-dimensional distribution, this means that all the points look like they're all just as similar to one another (because the high-dimensional kernels all look the same). In turn we have a tendency towards equidistance in the low dimensions, forcing the data to aggregate in a circular blob.

![t-sne out]({{ stie.url }}/assets/news-data-initial.png){: .center-image }

Nonetheless, there are some definitive clusters already. The extracted columns used were (with additional preprocessing):

{% highlight mma %}

{"SQLDATE", "EventCode", "QuadClass", "GoldsteinScale", "Actor1Name",
"Actor2Name", "IsRootEvent", "NumMentions", "NumSources",
"NumArticles", "AvgTone" , "Actor1Geo_FullName", 
"Actor2Geo_FullName", "URL"}

{% endhighlight mma %}