---
layout: post
category : class-posts
tagline:
tags : [exploratory-analysis,data]
---
{% include JB/setup %}

# Week 1: Exploratory analysis

{% highlight r %}
a
b
c
{% endhighlight %}

Our goal is to explain stock market behavior by relying on non-macroeconomic news sources. Using macroeconomic data is an obvious (and previously successful) approach, but using the highly diverse and worldwide event data from our dataset may reveal more interesting explanations for stock market behavior.

## Project description

Our goal will be to predict the stock market. As an initial simplification, we will be predicting the end-of-day value of S&P500 given its history and past world event history (looking back `N` days). Other dimensions to predict are volatility and correlations among assets themselves or between sectors.

The news source (GDELT) is lagged by one day, and it does not have timestamps for the news. Thus any model that we build can only be applied assuming there is an existing real-time scraper for the data.

The insufficiency in our data source here leads to other interesting directions we can take our analysis - we can train models to be lagged arbitrarily when simulating from historical events (or not at all). The predictive power of all of the models may vary. An important question is how much weaker long-term predictions (over several days or a month) are given minor delays (such as a day).

In the future, we may also look at other indices: DJIA, FTSE, DAX, Vanguard, etc. Finally, one more option would be to look at currency exchange rates. GDELT's wealth of international data would be very predictive here.

## The Data

#### GDELT 

This is a database of worldwide data. It contains a rich graph with parsed information, as well as a database with more rudimentary information. [Raw data link on GDELT site](data.gdeltproject.org/events/index.html).

Format

#### Stock Market Information

## Exploratory Analysis

#### Initial GDELT analysis

#### Initial stock analysis

#### Initial cross-dataset exploration

## Cool image example

![Dummy image]({{ stie.url }}/assets/dummy-image.jpg){: .center-image }

