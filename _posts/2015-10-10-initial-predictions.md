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

#### HMM review 

Clustering data points into: up trend / down trend
Feeding only the actual prices to the HMM is not sufficient, because the trends are not very clear in commodities (by opposition to stocks for example) => erratic predictions. We remedy to this problem by adding the moving average as input so that the model is more stable.


![XAU hmm out]({{ stie.url }}/assets/xau-hmm.png ){: .center-image }
![XAG hmm out]({{ stie.url }}/assets/xag-hmm.png ){: .center-image }

Train data in different clusters independently using a linear model.


