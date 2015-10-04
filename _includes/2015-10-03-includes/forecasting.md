Let's call $$f_t$$ our predictor at time $$t$$
$$f_t$$ is a function of all features observed before $$t$$

# Feature selection
- The moving average of 1 week, 1 month, 3 months to smooth the price time series, for different commodities
- The (real valued) output from the dimensionality reduction of the GDELT.

# Loss function
- $$L_2$$ error between the real price movements and the prediction of the estimator
- log loss

# Result validation
- Dividing the set in two: training and testing
- Cross validation doesn't always make sense (look ahead bias)

We can think about the 
At time $t$, let $B_t \in \mathbb{R}^d$ represent the data that we know at time $t$ (news events, past prices of commodities), and we want to find the best estimator $f$ of  
# Algorithms 




# Linear models
$$P^j_{t+1} = f(A_t \text{Data}_t) $$

## Optimize the likelihood of what we see

- LDA 
- Maximum likelihood

## Optimize the prediction power over the training set
- SVM
- Linear regression

# Non linear models
We want a probabilistic framework for modeling a time series 
- Hidden Markov Models
  - hidden states, output states, transition probabilities between states
  - hidden states will correspond to market regimes (bullish, bearish)
  - can experiment with subsets of output states: large change up, slight change up, large change down, slight change down, no change
  - given past time series data we want to input a new data point (some news event) which might change our regime/hidden state
  - use generalized expectation-maximization to compute maximum likelihood estimates and posterior estimates for transitions and outputs
  
- Genetic Algorithms
  - used to optimize parameters of neural net, decision tree, etc
  - 
- Neural network
