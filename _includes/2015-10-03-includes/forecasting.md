
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

### Algorithms 

#### Linear models
$$O^j_{t+1} := (x_{t+1}^{(i)} > x_t^{(i)}) = f(A_t \text{Data}_t) $$

##### Optimize the likelihood of what we see

- LDA, Maximum Likelihood ... : 
- $$\text{max} \mathbb{P} (\mathbb{Data}_t | O_t)$$

##### Optimize the prediction power over the training set
- Linear regression
- SVM: can also be non linear using a kernel 


#### Non linear models
- Hiden Markov Models
- Genetic Algorithms
- Neural network
