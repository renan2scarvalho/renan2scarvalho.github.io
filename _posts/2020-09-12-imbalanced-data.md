---
layout: post
title: Learning with Imbalanced Data
subtitle: Quick view over Imblanaced Data
thumbnail-img: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1024px-Python-logo-notext.svg.png
#cover-img: 
#share-img: 
gh-repo: renan2scarvalho/Skewed-Data
gh-badge: [star, fork, follow]
tags: [data science, python, data cleansing]
comments: true
---

As said before, real-world data is *messy*. There's no such thing as perfection dataset, and frequently one will face imbalanced data. But *what is imbalanced data*?

# Learning with Imbalanced Data

Imbalanced data is the case when classes have unequal numbers of samples. There's no standard of the exact degree of imbalancing, but as general rule, when the most common class is less than 1:2 as the rarest class, this would be only marginally unbalanced. Nevertheless, higher ratios such as 10:1 are already modestly imbalanced, whereas ratios above 1000:1 are extremely unbalanced. But the most important thing is how imbalance affects learning.

Costs of errors (cost functions) are often asymmetric and quite skewed (e.g. imbalance ratio 10:1 often has higher errors than 3:1), violating the assumption that they are uniform and thus only accuracy shold be optimized. Added to that, using accuracy as a metric of evaluation with imbalanced often has the impact of poor minority class performance traded-oof for improved majority class performance [[1]](https://www.wiley.com/en-us/Imbalanced+Learning%3A+Foundations%2C+Algorithms%2C+and+Applications-p-9781118074626). 

Here we'll take the [accuracy paradox](https://en.wikipedia.org/wiki/Accuracy_paradox) to exemplify how this can be an issue. A simple model may have a high level of accuracy but be too crude to be useful i.e. taking Class A with 99% of samples, predicting every case is Class A results in 99% of accuracy, which in this case does not reflect variance of Classes or nothing else but the underlying Class distribution.

Generally, we have three main issues with learning from imbalanced data:
1. **Problem issues:** happens when there's insufficient information to define the problem in a proper way, e.g. when there's no way to evaluate the learned knowledge;
2. **Data issues:** when there's not sufficient samples from on one or more classes to learn the rarities effectively (absolute rarity);
3. **Algorithm issues:** inadequacies in the algorithms which makes it learn poorly when imbalanced data exists.

Nevertheless, as [[1]](https://www.wiley.com/en-us/Imbalanced+Learning%3A+Foundations%2C+Algorithms%2C+and+Applications-p-9781118074626) proposes, class imbalances, i.e. relative differences in class proportions, is not fundamentally a problem at the data level, but rather a data distribution problem. So relative rarity remais as a problem formulation or algorithmic limitation issue. So the key here is that *imbalanced data is a problem only because learning algorithms cannot effectively handle this kind of data*.

So let's talk now how can one address those issues presented earlier:

1. *Problem issues*:
    - Use *appropriate evaluation metrics*, which means *changing the focus from accuracy oriented* only. Some other common metrics to be used are *Receiver Operation Characteristics (ROC)* (it does not have bias towards models that perform well on the majority class at expense of the majority class, as commented above on the accuracy paradox), and *precision and recall*, where recall is usually more used to assess the coverage of the minority class.
    - *Redefine the problem*, finding a subdomais where data is less imbalanced, but the subdomain is still of sufficient interest.
    
    
2. *Data issues*:
    - to deal with absolute rarity, the best approach would be acquire additional labeled data, preferentially from the rare classes or cases, which is not an easy work.
    - sampling methods, which are primarily used to address the problem with relative rarity, but do not address the problem with absolute rarity, so they just reducte between-class imbalance.
    
    
3. *Algorithm issues*:
    - use search methods that avoid greed and recursive partitioning (divide-and-conquer), since they have difficulty in finding rare patterns. One possibility is genetic algorithms;
    - Using search methods that involves evaluating metrics that properly value the learning, such as F-measure;
    - Using algorithms that implicitily of explicitily favor rare classes and cases, e.g. boosting in Adacost or SMOTEBoost;
    - Useing algorithms that learn only the rare class;

Let's just again make some distiction between *absolute rarity and relative rarity* [[2]](https://www.researchgate.net/publication/283282940_Rare_events_and_imbalanced_datasets_an_overview):
- Absolute rarity = lack of data: number of examples associated with the minority class is small in the absolute sense, so it's impossible to lear the decision boundaries associated with that class;
- Relative rarity = relative lack of data:  sometimes rare events or minority classes are not rare in the absolute sense, but they are rare relative to other events or classes, which makes it difficult to detect patterns associated with rare events or classes

Let's take this by examples. In the first case, consider that we have certain cancer data with 100 samples, and the minority class, let's say cancer = exists has a ratio of 5:100. Here the minority class is small in the absolute sense.
Now consider again a cancer dataset with 10,000 samples and same ratio. Here, the majority outnumbers the minority class, even though now 500 samples may not be considered rare.


















