---
layout: post
title: Working with Skewed Data
subtitle: Data Cleansing with Transformations
thumbnail-img: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1024px-Python-logo-notext.svg.png
#cover-img: 
#share-img: 
gh-repo: renan2scarvalho/Skewed-Data
gh-badge: [star, fork, follow]
tags: [data science, python, data cleansing]
comments: true
---

## Small intro

Real-world data can, and usually is, messy. Skewed distribution happens all the time, hence we need to work with this prior to modeling. A distribution
that is symmetric or nearly so is often easier to handle and interpret than a skewed distribution. Therefore, transformations are applied for many reasons, 
such as reduce skewness.

### But why is that?

More specifically, when removing skewness, transformations are attempting to make the dataset follow the Gaussian distribution.
The reason is simply that if the dataset can be transformed to be statistically close enough to a normmal or Gaussian dataset, then the largest set of
tools possible are available to them to use. Tests such as the ANOVA, t-test, F-test, and many others depend on the data having constant variance (σ²)
or follow a normal distribution.

As is know from statistics, long tails (or in other words skewed distributions) may act as outliers (as we will see ahead) for the statistical model, 
and several models use polynomial calculations on the predictor data i.e. labels, such as most linear models, neural networks, and SVM. 
Thus, in those cases, a skewed label distribution could have a negative effect over the models, since tails of distributions can dominate 
the underlying calculations, even though some models are robust to outliers (e.g. Tree-based), limiting the number of models that could be 
chosen [[1]](https://towardsdatascience.com/skewed-data-a-problem-to-your-statistical-model-9a6b5bb74e37).

So now let's begin!

## Hands-on!

The dataset used here is small sample of imdb movies. The primary steps here were to drop missing values (which are also a critical theme for data
cleansing), and select numerical variables. The image ahead shows the dataframe partially, which has 3756 samples and 16 predictors.

```javascript
df.shape
(3756, 16)
```
![df](https://user-images.githubusercontent.com/63553829/91345851-64d58380-e7b6-11ea-9670-ecb355ed8f80.png){: .mx-auto.d-block :}
