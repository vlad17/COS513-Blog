
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

The *assumption* after the "easy removals" section above is that each of the [columns](http://data.gdeltproject.org/documentation/GDELT-Data_Format_Codebook.pdf) contribute to the dissimilarity independently - so we only need to find per-column distances. It's unlikely that we can find the ideal distance function manually, so it's a good idea to parameterize it. These are going to end up being *hyperparameters* in the global regression problem, so we should be thrifty with them. One essential set of hyperparameters is the weights of each column. Other than that all the below functions can be extended to rely on additional hyperparameters if necessary.

We go through every column and define each columns own distance, each with its own parameters. For columns that are already numeric (e.g., `NumArticles`), that column's contribution is just the $$L_1$$ norm.

Categorical distances. We can probably come up with fancy distance metrics - `QuadClass` is one of {_Verbal Cooperation_, _Material Cooperation_, _Verbal Conflict_, _Material Conflict_}. One can make the argument that distance between "cooperation" is smaller than that between "verbal" and "material", but we must be careful not to inject unvalidated parameters into the function - e.g., distance between conflict/cooperation is 2 but between verbal/material is 1.

Thus, for now, categorical variables will just operate on the discrete metric.



-> Generate gram matrix
-> Discuss distance funciton
-> Preprocessing in python
-> Consider various reduction methods, their applicability, their assumptions (and whether they hold)
-> hyperparams and evaluation - over different $$d$$?
TODO: generalized selection algorithm to choose $$d$$?
-> whitening

algs: (new papers for new ones, old for old)
MDS
Isomap
LLE
PCA
sklearn (test out)