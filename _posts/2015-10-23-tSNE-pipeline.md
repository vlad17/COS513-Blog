---
layout: post
category : class-posts
tagline:
tags : [negative-results,t-SNE,HMM]
---
{% include JB/setup %}

# Week 4: tSNE Pipeline Results

This week's blog post will be a bit sparse - the paper draft was due today.

## The tSNE learning pipeline

Please see the final paper for details.

## Results

Mostly negative.

## Why?

Poor dimensionality reduction.

Nonrepresentative distance metric $$\rho$$. Expecting dimensionality reduction to do heavy lifting for us, but it can't without an infomative metric.

Linear-only features (no additional multiplications)

## Learned Lessons

1. Represent data fairly: Utilize more GDELT info and other columns. Categorical 0/1 simmilarity is influenced by an intuition that the separate categories are orthogonal, but they aren't, necessarily.
  * Cluster just news-topic related information. The intuition driving the clustering is that every day has a certain proportion of news topics reported. These shouldn't be confounded by factors that influence news importance - those are additional dimensions to consider.
  * Use a one-hot encoding. While this doesn't save us from the implicit orthognality assumption between categorical values that plagued $$\rho$$ before, it opens up the potential for features that are products of the on-hot encoding, capturing non-linear properties in the model. Furthermore, using the one-hot expansion and then performing classical whitening will help with the correlations.

2. Subsample to work with smaller data: learn clusters on a representative sample, then greatly reduce feature size by having topic-clustered representations for a day's events.

3. Inform the model of the historical data. Using pricing data independent of the event data (by using the HMM to classify up/down periods instead of providing a history to our GLM predictor) prevents the two predictors from interacting. We should abandon the HMM model for now, and instead focus on adding previous pricing features that are integrated with the news data in the GLM, again capturing non-orthogonal relationships.

Stay tuned for a new pipeline we developed to address these concerns!