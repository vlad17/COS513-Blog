### Singapore Paper Recap

[Forecasting Foreign Exchange Rates with Neural Networks](http://liawww.epfl.ch/uploads/project_reports/report_282.pdf)

#### Models

Four input models

- time-delayed daily averages
- time-delayed weekly averages
- progressive averages (6 months back): day before, two days, week, 2 weeks, month, 3 months, 6 months
- long progressive averages (2 years back)

Cannot just rely on NMSE, need to evaluate models using profit

Profit = (money obtained / seed money)^{52/w} - 1

Two strategies: buying and selling whole amount when increase/decrease predicted, and buying/selling only 10% at a time

#### Results

- Predictions often much lower than real value or lagged behind forex rates
- Predicting longer than 6 months is hard (NMSE and profit diverge wildly)
- Profit decreases when training model further
- Experimenting with large number of currencies without inputting linkage is not good
- Best profits achieved with time-delayed 14 daily averages (which was also worst in terms of NMSE)
- Long progressive averages model is best for NMSE
- Boolean indicators help with NMSE but have no effect on profit




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

We want to forecast the probability of direction changes using logit rather than linear functions, mainly because linear functions might result in degenerate solutions. Logit functions will map to [0,1], whereas a linear function might return something outside this range, unless we provide constraints. A linear function might still be useful for predicting probabilities within a general range of [0.2, 0.8], but will deviate greatly at extreme ranges, which we expect to encounter in our data. Whether or not the actual returns are linear or nonlinear is somewhat of an open question, with many types of models having been attempted with varying success.


#### Linear models
$$O^j_{t+1} := (x_{t+1}^{(i)} > x_t^{(i)}) = f(A_t \text{Data}_t) $$

##### Optimize the likelihood of what we see

- LDA, Maximum Likelihood ... 
$$\text{max} \mathbb{P} (\text{Data}_t | O_t)$$

##### Optimize the prediction power over the training set
- Linear regression
- [SVM](http://www.researchgate.net/publication/4047521_SVM_based_models_for_predicting_foreign_currency_exchange_rates): can also be non linear using a kernel 
 

###Other Techniques

#####Hidden Markov Models

  - [A stochastic HMM-based forecasting model for fuzzy time series](http://www.ncbi.nlm.nih.gov/pubmed/20028637)
  - Need to define our hidden states, output states, transition probabilities between states
  - Hidden states will correspond to market regimes (bullish, bearish)
  - Can experiment with subsets of output states: large change up, slight change up, large change down, slight change down, no change
  - Given past time series data we want to input a new data point (some news event) which might change our regime/hidden state
  - Use generalized expectation-maximization to compute maximum likelihood estimates and posterior estimates for transitions and outputs
  

##### Fuzzification
- We want to predict financial time series movement
- Is it enough to stop at predicting price up vs price down?
- [Fuzzification of Discrete Attributes From Financial Data in Fuzzy Classification Trees](http://www.researchgate.net/publication/224599514_Fuzzification_of_Discrete_Attributes_From_Financial_Data_in_Fuzzy_Classification_Trees)
- Piramuthu 1999, Kodogiannis and Lolis 2002, Lai et al 2009, Chen 2011

![Fuzzification Example](/assets/fuzzification_example.jpg){: .center-image }

- After fuzzification and prediction, defuzzify to retrieve nonfuzzy prediction
- Allows for human domain knowledge to influence system

#####Nearest Neighbor
  - [Fuzzy Nearest-Neighbor Method for Time Series Forecasting](http://www.smartquant.com/references/TimeSeries/ts4.pdf)
  - Encode time series as vector of change in direction
  - If $$x_{t} < x_{t-1} \implies -1$$, if $$x_{t} > x_{t-1} \implies 1$$, if $$x_{t} = x_{t-1} \implies 0$$
  - Fuzzify and normalize data
  - $$\mu(x_{i}) = [1 + \{d(x_{i}, x_{n}) / F(d)\}^{F_{e}}]^{-1.0}$$
  - Select nearest neighbors that have a fuzzy proximity within a certain $$\epsilon$$
  - Can also modify algorithm to have constraints where local movement around nearest neighbors must match prediction point
  - Singh achieved directional success of 85% with $$\epsilon = 0.1, 0.2$$ for product prices
  
#####Neural networks
  - [A new hybrid artificial neural networks and fuzzy regression model for time series forecasting](http://www.sciencedirect.com/science/article/pii/S0165011407004678)
  - [The application of neural networks to forecast fuzzy time series](http://ac.els-cdn.com/S0378437105008460/1-s2.0-S0378437105008460-main.pdf?_tid=54ef9840-6af5-11e5-8504-00000aacb35f&acdnat=1444003943_6f86d2513d2e05a4b041fde877b5b9c7)
  - Like the Fuzzy Nearest Neighbor, NNs have been adapted to use fuzzy logic
  - Select some membership function $$\mu(x)$$
  - Create rules based on membership values of time series points
    - If $$x = A_{1} $$ and $$y = B_{1} \implies f_{1} = p_{1}x + q_{1}y + r_{1}$$
    - If $$x = A_{2}$$ and $$y = B_{2} \implies f_{2} = p_{2}x + q_{2}y + r_{2}$$
    - $$A_{i}$$ and $$B_{i}$$ are fuzzy sets
   
  ![Fuzzy Neural Net](/assets/fuzzy_neural_net.jpg){: .center-image }

#####Genetic Algorithms
  - Used to optimize parameters of neural nets, decision trees, etc



