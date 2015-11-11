![kmeans-img](/assets/kmeans.jpg){: .center-image }

$$K$$-means defines hard clusters
GMMs are appealing because GMMs will generalize clustering to use information about the covariance of the data

Still, with GMMs and $$K$$-Means clustering, we need to do a parameter search for the number of $$K$$ clusters (or mixture components)

Don't have any intuitive sense for how many news clusters there are
Very hard to guess with 200k news events a day

Use infinite GMM (DPGMM)
Gaussian mixture model with Dirichlet process prior defined over mixture components
Predicts number of clusters while performing model inference
Number of clusters change to fit data
Works well when clusters are approximately Gaussian and well-defined
Not as good when distributions of clusters are skewed or tailed

Implementation uses $$\alpha$$ parameter for cluster hyperparameter
Set at 1 (Rasmussen uses 3.5)
Has $$N$$ to limit the maximum number of components
$$N=5000$$ since we want to approximate an infinite model

![kmeans-img](/assets/igmm.png){: .center-image }

