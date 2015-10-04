
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

Define a distance $$\rho:R\times R\rightarrow \mathbb{R}_+$$. $$\rho$$ induces the Gram matrix $$G\in\mathbb{R}^{N\times N}$$ for our data. Treating $$G$$ as our distance matrix enables us to apply (albeit with less efficient algorithms) classical dimensionality reduction techniques.

The **assumption** after the "easy removals" section above is that each of the [columns](http://data.gdeltproject.org/documentation/GDELT-Data_Format_Codebook.pdf) contribute to the dissimilarity independently - so we only need to find per-column distances. It's unlikely that we can find the ideal distance function manually, so it's a good idea to parameterize it. These are going to end up being *hyperparameters* in the global regression problem, so we should be thrifty with them. One essential set of hyperparameters is the weights of each column. Other than that all the below functions can be extended to rely on additional hyperparameters if necessary.

We go through every column and define each columns own distance, each with its own parameters. For columns that are already numeric (e.g., `NumArticles`), that column's contribution is just the $$L_1$$ norm.

Categorical distances. We can probably come up with fancy distance metrics - `QuadClass` is one of {_Verbal Cooperation_, _Material Cooperation_, _Verbal Conflict_, _Material Conflict_}. One can make the argument that distance between "cooperation" is smaller than that between "verbal" and "material", but we must be careful not to inject unvalidated parameters into the function - e.g., distance between conflict/cooperation is 2 but between verbal/material is 1.

Thus, for now, categorical variables will just operate on the discrete metric.

Example of **future potential improvement**: look up location data - in addition to a discrete metric, "New York" and "Connecticut" string locations would have additional columns for their lattitude and longitude (which would have to be looked up).

#### Data Size

Important to consider data size for feasibility of methods. For initial approaches, we're going to use whole Gram-matrix representations, so we need to hold all $$N^2$$ doubles in memory. At `8B` for a packed double, when we're testing on our machines, we're already limited to `sqrt(8GB/8B)`, or only about `~30K` rows!

Remember every recent day is about `250K` rows.

Solutions:

* Most recent `~100` rows, then **randomly sample** (we can weigh sampling by a function of the columns, since this is a linear-in-size operation) from previous days' rows.
* Use `cycles.cs.princeton.edu`, with `256GB` ram, boosting us to `~100K` rows.
* Download more RAM.
* If manual distance can be specified, just do disk seeks for distance searches. If this is too slow, we can sort files and then make them smaller. If this is too slow, we can use a SQLite instance.

Last one is a lot of work, but probably the most enabling.

#### Dimensionality Reduction Algorithms

Only have distance to work with, and no intuition for the manifold of event space. Will have to try various embedding approaches.

1. PCA
2. MDS
3. LLE
4. Isomap

TODO: test out sklearn

#### Evaluation Algorithm

Let us presume a probabilistic forcast $$f$$ from above. 

**Matrix-based evaluation**.

_Outer loop_: Take $$N'$$ most recent rows and sample $$N$$ more from $$M$$ months ago. Run dimensionality reduction algorithm $$i$$ from above.

_Inner loop:_ Binary search on sub-dimension $$d$$ (just a bias-variance tradeoff as we go low-to-high dimension, perhaps use F-score here in the binary search). Use the output of $$i$$ on a given $$d$$ to train and validate $$f$$, obtaining some loss value.

Optimize the _inner loop_ (logarithmic search over values $$1,...50$$) over hyperparameters $$N,N',M,i$$. $$i\in\{1,...,4\}$$ is small enough, but $$N,N',M$$ need to be segmented (since they need to sweep a large range).

**Distanced-based evaluation**.

The only difference here is we're looking up the rows and recomputing distance each time it is queried, not explicitly representing it. The outer loop's parameters can be simplified to us the last $$N$$ rows, where $$N$$ may now be much larger.


