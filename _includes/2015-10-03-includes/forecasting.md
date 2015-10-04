TODO: mention insights from this again
[Forecasting Foreign Exchange Rates with Neural Networks](http://liawww.epfl.ch/uploads/project_reports/report_282.pdf)




### Feature selection
- The moving average of 1 week, 1 month, 3 months to smooth the price time series, for different commodities
- The (real valued) output from the dimensionality reduction of the GDELT.

### Loss function
- $$L_2$$ error between the real price movements and the prediction of the estimator
- Logarithmic Loss

### Result validation
- Dividing the set in two: training and testing
- Cross validation doesn't always make sense (look ahead bias)
- Develop a trading strategy and use on the market

We can think about the 
At time $t$, let $B_t \in \mathbb{R}^d$ represent the data that we know at time $t$ (news events, past prices of commodities), and we want to find the best estimator $f$ of  
# Algorithms 

=======
### Algorithms 
(TODO: note: logit probably more natural than linear in probabilistic sense)
#### Linear models
$$O^j_{t+1} := (x_{t+1}^{(i)} > x_t^{(i)}) = f(A_t \text{Data}_t) $$

##### Optimize the likelihood of what we see

- LDA, Maximum Likelihood ... :  $$\text{max} \mathbb{P} (\text{Data}_t | O_t)$$

##### Optimize the prediction power over the training set
- Linear regression
- [SVM]{http://www.researchgate.net/publication/4047521_SVM_based_models_for_predicting_foreign_currency_exchange_rates}: can also be non linear using a kernel 

# Non linear models
We want a probabilistic framework for modeling a time series 
- Hidden Markov Models
  - hidden states, output states, transition probabilities between states
  - hidden states will correspond to market regimes (bullish, bearish)
  - can experiment with subsets of output states: large change up, slight change up, large change down, slight change down, no change
  - given past time series data we want to input a new data point (some news event) which might change our regime/hidden state
  - use generalized expectation-maximization to compute maximum likelihood estimates and posterior estimates for transitions and outputs
  
=======
#### Non linear models
- Hiden Markov Models

- Genetic Algorithms
  - used to optimize parameters of neural net, decision tree, etc
  - 
- Neural network
