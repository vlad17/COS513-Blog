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

### Algorithms 
(TODO: note: logit probably more natural than linear in probabilistic sense)
#### Linear models
$$O^j_{t+1} := (x_{t+1}^{(i)} > x_t^{(i)}) = f(A_t \text{Data}_t) $$

##### Optimize the likelihood of what we see

- LDA, Maximum Likelihood ... :  $$\text{max} \mathbb{P} (\text{Data}_t | O_t)$$

##### Optimize the prediction power over the training set
- Linear regression
- [SVM]{http://www.researchgate.net/publication/4047521_SVM_based_models_for_predicting_foreign_currency_exchange_rates}: can also be non linear using a kernel 

#### Non linear models
- Hiden Markov Models
- Genetic Algorithms
- Neural network
