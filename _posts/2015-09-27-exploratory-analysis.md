---
layout: post
category : class-posts
tagline:
tags : [exploratory-analysis,data]
---
{% include JB/setup %}

# Week 1: Exploratory analysis

Our goal is to explain stock market behavior by relying on non-macroeconomic news sources. Using macroeconomic data is an obvious (and previously successful) approach, but using the highly diverse and worldwide event data from our dataset may reveal more interesting explanations for stock market behavior.

## Project description

Our goal will be to predict the stock market. As an initial simplification, we will be predicting:
 
* the end-of-day (close) value of S&P500 (team 1) and commodities (team 2)
* volatility and correlations among assets themselves or between sectors.

Where we use as given given its history and past world event history (looking back `N` days).

The news source (GDELT) is lagged by one day, and it does not have timestamps for the news. Thus any model that we build can only be applied assuming there is an existing real-time scraper for the data.

The insufficiency in our data source here leads to other interesting directions we can take our analysis - we can train models to be lagged arbitrarily when simulating from historical events (or not at all). The predictive power of all of the models may vary. Thus, if we have time, an interesting third direction we may explore is

* How much weaker long-term predictions (over several days or a month) are given minor delays of information (such as a day). 

## Previous Work
Previous work as looked at forecasting various financial data with a broad array of techniques from traditional machine learning approaches to finding new feature sets from Wikipedia, Google, and the news.

- [Stock Prices, News, and Business Conditions](http://www.nber.org/papers/w3520.pdf)
  - Analyzing the effect of news on stock prices: ''Fundamental macroeconomic news has little effect on stock prices... Effect of news... depends on the varying responses of expected flows relative to equity discount rates'' 
- [Big Data Gets Bigger: Now Google Trends Can Predict The Market](http://www.forbes.com/sites/davidleinweber/2013/04/26/big-data-gets-bigger-now-google-trends-can-predict-the-market/)
  - Using Internet search data to predict pricing data: ''the team found a strong correlation between Internet searches for a company?s name and its trade volume, the total number of times the stock changed hands over a given week... But the Google data couldn't predict its price'' 
- [Forecasting Foreign Exchange Rates with Neural Networks](http://arxiv.org/pdf/1404.1996v1.pdf)
- [Visual and Predictive Analytics on Singapore News: Experiments on GDELT, Wikipedia, and ^STI](http://liawww.epfl.ch/uploads/project_reports/report_282.pdf)
  - Using GDELT ''to highlight the various impacts of June 2013
Southeast Asian haze and December 2013 Little India riot on Singapore'' 
- [SVM based models for predicting foreign currency exchange rates](http://www.researchgate.net/publication/4047521_SVM_based_models_for_predicting_foreign_currency_exchange_rates)
  - Using SVMs to predict foreign exchange rates 

## The Data

### GDELT 

This is a database of worldwide data. 
[Raw data link on GDELT site](http://data.gdeltproject.org/events/index.html).


The Global Database of Events, Language, and Tone (GDELT) is the largest, highest resolution, and most detailed open dataset of global human society. 
It is freely available and uses a variety of international news sources with daily updates, contains more than 250 million events from 1979 to present, is CAMEO-coded (for simple indexing of events) and regularly introduces new enhancements to the data.


To achieve that, GDELT Project monitors the world's broadcast, print, and web news, from nearly every corner of every country in over 100 languages and identifies  the people, locations, organizations, counts, themes, sources, emotions, counts, quotes and events driving our global society every second of every day. 


The GDELT provides a knowledge graph that describes not only “what” happened but “how” it happened. Accordingly,  as the GDELT Global Knowledge Graph processes each news article it extracts a list of all people, organizations, locations, and themes from that article and concatenates them together to form a unique "key" that represents that particular combination of names, locations, and themes. All articles containing that same unique combination of names, locations, and themes, regardless of how similar the rest of the text is, are grouped together into a "nameset". The Graph provides a daily intensity weighting which essentially weights each day towards those that occur in the greatest diversity of contexts, biasing towards days with many different contexts being discussed. It is relatively immune to sudden massive bursts of coverage (such as from a major sudden situation) and instead tends to capture the broadest temporal trends. This can be used to evaluate the magnitude of events/turmoil witness each day, in every country.


![Turmoil in the first 7 days of July](/assets/First_Week_July_15.PNG){: .center-image }

#### Data Volume

GDELT releases a raw data file every day that average about **250k rows (approx. 3 rows per second)**.

#### Data Schema

The row data file is a csv with around 57 columns that indicate several variables such as the:

* [Event CAMEO code](http://data.gdeltproject.org/documentation/CAMEO.Manual.1.1b3.pdf)
* Date timestamp
* Location
* Link to articles
* Actors involved

The CAMEO code enables for numeric categorization of events. Since the dimensionality of ``events that can occur" is very large, the CAMEO system enables compactification and reduction of this space into something more analyzable. CAMEO improves upon previous event categorization schemes, WEIS and COPDAB. Features:

1. It relies on WordNet synsets to automatically codify events
2. Supports state and non-state actors, as well as regional and ethnic categorization.
3. Location information makes sense in the context of a cameo label

![Example CAMEO label]({{ stie.url }}/assets/cameo-example.png){: .center-image }

## Exploratory Analysis

We retrieved stock pricing information from Yahoo Finance for S&P 500 (^GSPC) from January 1, 2005 to December 31, 2005. This data included the S&P's opening, high, low, closing, and adjusted closing price for each date in this range. We also looked at GDELT data for 2005. 

### Delving into GDELT

TODO explore CAMEO space

## R Exploration

Import libraries

{% highlight r %}

library(ggplot2)
library(dplyr)
library(tidyr)
library(lattice)
library(quantmod)

{% endhighlight %}

Read the GDELT news data from 2005

{% highlight r %}

news_data <- read.csv("gdelt_2005.csv", header=FALSE, sep='\t') # read data
news_data$date <- as.Date(as.character(news_data$SQLDATE), "%Y%m%d")
colnames(news_data) <- c("GLOBALEVENTID", "SQLDATE", "MonthYear", "Year", "FractionDate", "Actor1Code", "Actor1Name", "Actor1CountryCode", "Actor1KnownGroupCode", "Actor1EthnicCode", "Actor1Religion1Code", "Actor1Religion2Code", "Actor1Type1Code", "Actor1Type2Code", "Actor1Type3Code", "Actor2Code", "Actor2Name", "Actor2CountryCode", "Actor2KnownGroupCode", "Actor2EthnicCode", "Actor2Religion1Code", "Actor2Religion2Code", "Actor2Type1Code", "Actor2Type2Code", "Actor2Type3Code", "IsRootEvent", "EventCode", "EventBaseCode", "EventRootCode", "QuadClass", "GoldsteinScale", "NumMentions", "NumSources", "NumArticles", "AvgTone", "Actor1Geo_Type", "Actor1Geo_FullName", "Actor1Geo_CountryCode", "Actor1Geo_ADM1Code", "Actor1Geo_Lat", "Actor1Geo_Long", "Actor1Geo_FeatureID", "Actor2Geo_Type", "Actor2Geo_FullName", "Actor2Geo_CountryCode", "Actor2Geo_ADM1Code", "Actor2Geo_Lat", "Actor2Geo_Long", "Actor2Geo_FeatureID", "ActionGeo_Type", "ActionGeo_FullName", "ActionGeo_CountryCode", "ActionGeo_ADM1Code", "ActionGeo_Lat", "ActionGeo_Long", "ActionGeo_FeatureID", "DATEADDED")
news_data_ts <- xts(news_data[c("GoldsteinScale", "NumMentions", "AvgTone")], order.by=news_data$date)
news_data_mean_ts <- aggregate(news_data_ts, index(news_data_ts), "mean")

{% endhighlight %}

Read the S&P 500 price data from 2005

{% highlight r %}

price_data <- read.csv("sp500_2005.csv", header=TRUE, sep=',') # read data
price_data <- as.data.frame(price_data)
price_data_ts <- xts(price_data[,-1], order.by=as.Date(price_data$Date))
chartSeries(price_data_ts, name="S&P 500")

{% endhighlight %}

Histograms for the Goldstein Scale, Number of Mentions and Average Tone of news articles

{% highlight r %}

# Histogram for Goldstein Scale
ggplot(news_data,aes(x=GoldsteinScale)) + geom_histogram(binwidth=0.5)

{% endhighlight %}

![Goldstein scale filename]({{ stie.url }}/assets/Histogram_Goldstein_Scale.png){: .center-image }

{% highlight r %}

# Histogram for Number of Mentions
ggplot(news_data[news_data$NumMentions<30,],aes(x=NumMentions)) + geom_histogram(binwidth=1)

{% endhighlight %}

![Num mentions filename]({{ stie.url }}/assets/Histogram_Num_Mentions.png){: .center-image }
{% highlight r %}

# Histogram for Average Tone
# ggplot(news_data,aes(x=AvgTone)) + geom_histogram()

{% endhighlight %}


Histograms for daily return of S&P 500 price

{% highlight r %}

# Histogram for Daily Return
price_ret <- log(price_data_ts$Adj.Close) - log(lag(price_data_ts$Adj.Close, 1))
colnames(price_ret) <- "DailyReturn"
ggplot(price_ret,aes(x=DailyReturn)) + geom_histogram()

{% endhighlight %}

![Daily return filename]({{ stie.url }}/assets/Histogram_Daily_Return.png){: .center-image }

Volume of trading vs Average Number of Mentions

{% highlight r %}

# Volume of trading vs Average Number of Mentions
merged_data <- merge(price_data_ts$Volume, news_data_mean_ts)
plot(coredata(merged_data$Volume), coredata(merged_data$NumMentions))

{% endhighlight %}

![Daily return filename]({{ stie.url }}/assets/Volume_vs_Num_Mentions.png){: .center-image }

There seems to be almost no correlation between the number of mentions and the price data.
