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

**t-Distributed Stochastic Neighbor Embedding**

Double-encoded MNIST (from paper, had PCA run initially to 50 dims for speed):

![t-sne mnist]({{ stie.url }}/assets/tsne_map.png){: .center-image }

Byte-encoded MNIST (t-SNE only in [my fork](https://github.com/vlad17/bhtsne/tree/mnist)), `10000 imgs x (28x28) pixels` trains in 164s:

![t-sne out]({{ stie.url }}/assets/mnist-color.png){: .center-image }

News data reduction. 2 days' worth of data - `326604 x 14` data matrix, with categories. Took <2 hrs. Just two instantiations for parameters, perplexity $$u = 30$$ for the high-dimensional distribution and cutoff $$\theta = 0.5$$ were used.

![t-sne out]({{ stie.url }}/assets/news-data-initial.png){: .center-image }

Extracted columns (with additional preprocessing):

{% highlight mma %}

{"SQLDATE", "EventCode", "QuadClass", "GoldsteinScale", "Actor1Name",
"Actor2Name", "IsRootEvent", "NumMentions", "NumSources",
"NumArticles", "AvgTone" , "Actor1Geo_FullName", 
"Actor2Geo_FullName", "URL"}

{% endhighlight mma %}