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

This is a database of worldwide data. 
[Raw data link on GDELT site](data.gdeltproject.org/events/index.html).


The Global Database of Events, Language, and Tone (GDELT) is the largest, highest resolution, and most detailed open dataset of global human society. 
It is freely available and uses a variety of international news sources with daily updates, contains more than 250 million events from 1979 to present, is CAMEO-coded (for simple indexing of events) and regularly introduces new enhancements to the data.


To achieve that, GDELT Project monitors the world's broadcast, print, and web news, from nearly every corner of every country in over 100 languages and identifies  the people, locations, organizations, counts, themes, sources, emotions, counts, quotes and events driving our global society every second of every day. 


The GDELT provides a knowledge graph that describes not only “what” happened but “how” it happened. Accordingly,  as the GDELT Global Knowledge Graph processes each news article it extracts a list of all people, organizations, locations, and themes from that article and concatenates them together to form a unique "key" that represents that particular combination of names, locations, and themes. All articles containing that same unique combination of names, locations, and themes, regardless of how similar the rest of the text is, are grouped together into a "nameset". The Graph provides a daily intensity weighting which essentially weights each day towards those that occur in the greatest diversity of contexts, biasing towards days with many different contexts being discussed. It is relatively immune to sudden massive bursts of coverage (such as from a major sudden situation) and instead tends to capture the broadest temporal trends. This can be used to evaluate the magnitude of events/turmoil witness each day, in every country.


GDELT releases a raw data file every day that average 250k rows (3 rows per second). The row data file is a csv with around 57 columns that indicate several variables such as the Event CAMEO code, timestamp, location, link to articles, persons involved, etc ....


Format

#### Stock Market Information

## Exploratory Analysis

#### Initial GDELT analysis

#### Initial stock analysis

#### Initial cross-dataset exploration

## Cool image example

![Dummy image]({{ stie.url }}/assets/dummy-image.jpg){: .center-image }

