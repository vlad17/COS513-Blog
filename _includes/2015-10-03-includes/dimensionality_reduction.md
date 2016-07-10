
### Reducing Categorical Data

#### Easy Removals

Some columns are redundant or don't provide info:

* `GLOBALEVENTID` -- removed
* `SQLDATE`
* `MonthYear` -- removed
* `Year` -- removed
* `FractionDate` -- removed
* `DATEADDED` -- removed
* `EventCode` -- `EventRootCode` and `EventBaseCode` are derived from it. CAMEO codes have three levels, and we want to be able to capture that lexicographic structure (decompose into 3-tier levels).
* `URL` -- contains lots of trash; only use domain name for credibility detection.

#### Categorical Data Dimensionality Reduction

We will find it useful to define two functions, not necessarily related to each other:

* Define a metric $$\rho:R\times R\rightarrow \mathbb{R}_+$$
* Define an inner product $$\langle\cdot,\cdot\rangle:R\times R\rightarrow\mathbb{R}$$

The inner product induces the similary matrix $$G\in\mathbb{R}^{M\times M}$$.

The **assumption** after the "easy removals" section above is that each of the [columns](http://data.gdeltproject.org/documentation/GDELT-Data_Format_Codebook.pdf) contribute to the similarity and distance independently - so we only need to find per-column distances and similarities. It's unlikely that we can find the ideal distance function manually, so it's a good idea to parameterize it. These are going to end up being *hyperparameters* in the global regression problem, so we should be thrifty with them. One essential set of hyperparameters is the weights of each column. Other than that all the below functions can be extended to rely on additional hyperparameters if necessary.

We go through every column and define each columns own distance or similarity, each with its own parameters. For columns that are already numeric (e.g., `NumArticles`), that column's contribution is just the $$L_1$$ norm.

Categorical distances. We can probably come up with fancy distance metrics - `QuadClass` is one of {_Verbal Cooperation_, _Material Cooperation_, _Verbal Conflict_, _Material Conflict_}. One can make the argument that distance between "cooperation" is smaller than that between "verbal" and "material", but we must be careful not to inject unvalidated parameters into the function - e.g., distance between conflict/cooperation is 2 but between verbal/material is 1.

Thus, for now, categorical variables will just operate on the **discrete metric** (with similarity being equal to distance).

Example of **future potential improvement**: look up location data - in addition to a discrete metric, "New York" and "Connecticut" string locations would have additional columns for their lattitude and longitude (which would have to be looked up).

#### Data Size

It is important to consider data size for feasibility of methods. For initial approaches, we're going to use whole matrix representations, so we need to hold all $$N^2/2$$ doubles in memory. At `8B` for a packed double, when we're testing on our machines, we're already limited to `2*sqrt(8GB/8B)`, or only about `~60K` rows!

Remember every recent day is about `200K` rows.

Solutions:

* Most recent `~100` rows, then **randomly sample** (we can weigh sampling by a function of the columns, since this is a linear-in-size operation) from previous days' rows. Then we would sample about `60K` rows
* Use `cycles.cs.princeton.edu`, with `256GB` ram, boosting us to `~200K` rows, but this is still barely a day's worth. Would need to use sampling again.
* Read data into a `LevelDB` instance, then reply to distance/dissimilarity queries on demand.

Last one is a lot of work, but probably the most enabling. Note it does us pretty much no good if the algorithm needs to use $$O(M^2)$$ space.

#### Linear or Locally Linear Approaches

Linear dimensionality reduction approaches all rely on finding a projection matrix $$P$$ and then offering $$PX$$ as the reduced data. LLE is somewhat similar (see below).

1. **Principal Components Analysis**

    [Cunningham and Ghahramani 2015, Section 3.1.1](http://stat.columbia.edu/~cunningham/pdf/CunninghamJMLR2015.pdf). Classical PCA minimizes the sum of the squares of the residuals over all projections of the data into a low dimensional space. Usual matrix solution is to take SVD of $$G=Q\Lambda Q^\top$$, then the first $$r$$ columns.

    *Works well when* data lie near (in terms of square distance) a low-dimensional hyperplane.

    If PCA works the best out of all of these, we can extend to Probabilistic or Bayesian PCA.

2. **Multidimensional Scaling**

    [Cunningham and Ghahramani 2015, Section 3.1.2](http://stat.columbia.edu/~cunningham/pdf/CunninghamJMLR2015.pdf). MDS maximizes scatter (sum of pairwise distances). Optimizes

    $$\min_{\textbf{s}_i}\sum_{i,j}\left(\|\textbf{s}_i-\textbf{s}_j\|_2-\rho(\textbf{r}_i,\textbf{r}_j)\right)^2$$

    *Works well when* that the most of the magnitude of the difference vectors between points lies in the vector spaced produced by the solution hyperplane.

    Incremental MDS algorithms exist - this helps avoid quadratic space cost (on-demand $$\rho$$) but the runtime is still superquadratic.

3. **Locally Linear Embedding**

    [Roweis and Saul 2000](http://www.sciencemag.org/content/290/5500/2323). LLE is a nonlinear method which maintains the following invariant: for each data point's closest $$K$$ neighbors, the local linear approximation of the original point is maintained after the mapping.

    Assumes that the points lie near some differentiable manifold in the the high-dimensional space, and performs well for manifolds isomorphic to a hyperplane.

**Uh-oh!** Note we only have $$G=XX^\top$$, not $$X$$, to work with! So even if we find $$P$$, what would we apply it to? We need some initial mapping $$k:R\rightarrow\mathbb{R}^D$$ which respects $$G$$. At that point the reduction is just $$PK$$, where $$\boldsymbol{\delta}_n^\top K=k(\textbf{r}_n)^\top$$. $$D$$ should be approximately equal to the number of columns (our original dimension), but it's a pretty hard problem to reconstruct $$X$$ for small $$D$$ from $$G$$.

If $$D=M$$, it's easy - that's just the Cholesky Decomposition. However, then our data matrix is just as large as our Gram matrix, and this isn't very scalable.

This leaves us with the following options:

* For small $$M$$, during our exploratory stage, just use the entire $$M\times M$$ data matrix, since we're holding $$G$$ in memory already anyway.
* Bite the bullet, and use a one-hot encoding for our categories. **We have a lot of categories**. The URL column and actor names, even if we clean the URL, will contribute to having on the order of 20K columns - an all-double encoding means `160KB` *per row* - a smarter 1-bit encoding is about `12KB`. Overall for 200 days we'd need `0.5TB`!
* Use [Rectangular Cholesky Decomposition](https://commons.apache.org/proper/commons-math/apidocs/src-html/org/apache/commons/math3/linear/RectangularCholeskyDecomposition.html) for an *approximate* reconstruction in $$\mathbb{R}^D$$ for $$D$$ approximately equal to number of columns. This algorithm has a runtime of $$O(dM^2)$$, with operations being array accesses and math (and this means DB/disk accesses). Even an optimized solution in `C++` would take on the order of $$(200\, \text{days} \times 200\, \text{Krows/day})^2 \times 10\, \text{us} = 1000\, \text{days}$$ ([LevelDB random access time](https://github.com/google/leveldb)).

Basically, we're going to have to subsample no matter what. It's probably possible to massively parallelize these computation, but that would be a tremendous engineering effort.

#### Neighborhood Embedding

1. **t-Distributed Stochastic Neighbor Embedding**

   [L.J.P. van der Maaten 2014](http://lvdmaaten.github.io/tsne/). Probably, coupled with `LevelDB` database layer and a query-on-demand distance function $$\rho$$, this is our best bet. t-SNE finds an embedding in $$O(M \log M)$$ time in $$O(M)$$ memory.

   t-SNE works by minimizing the KL-divergence between two distributions: $$P$$, which represents pairwise similarity between data points of the high dimensional data, and $$Q$$, for the low dimensional data. Minimizing $$KL(P\|Q)$$ then gives us the distribution that requires the least amount of additional bits of information to infer pairwise similarity to $$P$$ - like an informational analogue to MDS.

   $$P$$ and $$Q$$ are defined in the paper - they are distributions over the *point similarities* themselves - in other words, $$p_{ij}$$ is the probability that points $$i,j$$ are similar, defined with ratios of Gaussian kernels of $$i,j$$ to the kernels with everything else (see paper for exact definition). $$q_{ij}$$ uses a heavier-tailed Student-t kernel to allow for far points in the high dimensions to be further appart in the lower dimension without as much KL penality. This helps address the curse of high dimensionality and focus on local structure.

Looks like our best bet, and enables us to use the full data set without subsampling.

#### Isomap

General approach, really it just transforms $$\rho$$ into $$\rho'$$, and can be used in addition to t-SNE or MDS.

[Tenenbaum, Silva, and Langford 2000](http://wearables.cc.gatech.edu/paper_of_week/isomap.pdf). Isomap works by taking a neighborhood graph, then recomputing distances based on the shortest path through the neighborhood graph, and then applying classical reduction techniques. Thus we can only traverse to nodes through neighbors. This enlarges distances to be more "intrinsic" to our manifold:

![spiral]({{ BASE_PATH }}/assets/spiral.png){: .center-image }

Isomap doesn't have the same requirement of hyperplane isomorphism that LLE does, but needs a shortest path computation over all nodes for the new metric it uses. This is done by the Floyd–Warshall algorithm, but in $$O(M^3)$$.

#### Implementation

Python's `sklearn` has all of the above methods (except t-SNE, which has optimized CUDA implementations). Given that we have to deal with quadratic runtime in most of the preprocessing methods, the runtime of (1) and (2) is not a big deal. If we go with the column-based approach, then data-construction is linear, in which case maybe only LLE is tractable.

#### Evaluation Algorithm

Let us presume a probabilistic forcast $$f$$ from above. 

**Matrix-based evaluation**.

_Outer loop_: Take $$N'$$ most recent rows and sample $$N$$ more from $$M$$ months ago. Run dimensionality reduction algorithm $$i$$ from above.

_Inner loop:_ Binary search on sub-dimension $$d$$ (just a bias-variance tradeoff as we go low-to-high dimension, perhaps use F-score here in the binary search). Use the output of $$i$$ on a given $$d$$ to train and validate $$f$$, obtaining some loss value.

Optimize the _inner loop_ (logarithmic search over values $$1,...50$$) over hyperparameters $$N,N',M,i$$. $$i\in\{1,...,4\}$$ is small enough, but $$N,N',M$$ need to be segmented (since they need to sweep a large range).

**Distanced-based evaluation**.

The only difference here is we're looking up the rows and recomputing distance each time it is queried, not explicitly representing it. The outer loop's parameters can be simplified to us the last $$N$$ rows, where $$N$$ may now be much larger.


