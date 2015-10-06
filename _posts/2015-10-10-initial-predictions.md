---
layout: post
category : class-posts
tagline:
tags : [exploratory-analysis,data,methods,dimensionality-reduction,logit,hmm,leveldb,t-sne]
---
{% include JB/setup %}

# Week 3: Initial Predictions

## LevelDB Impl

## Dimensionality Reduction Process

#### t-SNE Review

TODO: convert below into more math-heavy overview, then talk about implementaiton (leveldb client)

**t-Distributed Stochastic Neighbor Embedding**

   [L.J.P. van der Maaten 2014](http://lvdmaaten.github.io/tsne/). Probably, coupled with `LevelDB` database layer and a query-on-demand distance function $$\rho$$, this is our best bet. t-SNE finds an embedding in $$O(M \log M)$$ time in $$O(M)$$ memory.

   t-SNE works by minimizing the KL-divergence between two distributions: $$P$$, which represents pairwise similarity between data points of the high dimensional data, and $$Q$$, for the low dimensional data. Minimizing $$KL(P\|Q)$$ then gives us the distribution that requires the least amount of additional bits of information to infer pairwise similarity to $$P$$ - like an informational analogue to MDS.

   $$P$$ and $$Q$$ are defined in the paper - they are distributions over the *point similarities* themselves - in other words, $$p_{ij}$$ is the probability that points $$i,j$$ are similar, defined with ratios of Gaussian kernels of $$i,j$$ to the kernels with everything else (see paper for exact definition). $$q_{ij}$$ uses a heavier-tailed Student-t kernel to allow for far points in the high dimensions to be further appart in the lower dimension without as much KL penality. This helps address the curse of high dimensionality and focus on local structure.

![t-sne mnist]({{ stie.url }}/assets/tsne_map.png){: .center-image }