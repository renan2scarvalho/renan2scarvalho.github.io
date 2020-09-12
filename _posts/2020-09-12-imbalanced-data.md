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

So now that we have an idea of *what's imblanaced data*, let's check out some data sampling methods that try to overcome this issue! For that, we'll use the [Estonia ferry disaster](https://www.kaggle.com/christianlillelund/passenger-list-for-the-estonia-ferry-disaster) dataset from Kaggle, focusing mainly in the binary target "Survived", which had 989 passengers, with 137 survivors and 852 deaths, which represents approximately an imbalance ratio of 1:6, and using the quantitative predictor "Age" as the comparison between before and after resampling.

![image](https://user-images.githubusercontent.com/63553829/93004221-64115100-f51b-11ea-8f3e-e319e55be655.png){: .mx-auto.d-block :}

PS: Here we'll cover sampling techniques only, not machine learning algorithms. And now, **hands-on!**

# Resampling Techniques

Not always is possible to get new data, specially for the rare cases. So here we'll try to see some approaches to strive with imbalanced data through **resampling**.
Resampling basically involves creating a new transformed version of the training dataset with different class distribution, and in this case, randomly.

Here we'll use the *imblearn* library [[3]](https://imbalanced-learn.readthedocs.io/en/stable/user_guide.html), a specific library used for dealing with imbalanced classes. 

## Oversampling

Oversampling methods duplicate examples in the minority class or synthesize new examples from the examples in the minority class. Some of the techniques that will be covered here are [[4]](https://machinelearningmastery.com/data-sampling-methods-for-imbalanced-classification/):
1. Random Oversampling
2. Synthetic Minority Oversampling Technique (SMOTE)
3. Borderline-SMOTE
4. Borderline Oversampling with SVM
5. SMOTENC
6. Adaptative Synthetic Sampling (ADASYN)

### Random Oversampling

As commented above, in random over-sampling a random set of copies of minority class examples is added to the data. 

One advantage is that it leads to no information loss like under-sampling (reduced dataset). Nevertheless, this can increase the likelihood of overfitting, specially for high rates, hence decrease the classifier performance while increasing the computational effort [[5]](https://arxiv.org/pdf/1505.01658.pdf), [[6]](https://www.analyticsvidhya.com/blog/2017/03/imbalanced-data-classification/#:~:text=Dealing%20with%20imbalanced%20datasets%20entails,as%20it%20has%20wider%20application.).

```javascript
from imblearn.over_sampling import RandomOverSampler

oversample = RandomOverSampler(sampling_strategy='minority', random_state=42)
X_over, y_over = oversample.fit_resample(X,y)
```

The oversampling increased the number os samples from 989 to 1704, with 852 survivals and 852 deaths. Ahead we see the new "Age" distribution:

![oversamp](https://user-images.githubusercontent.com/63553829/93004424-2ca3a400-f51d-11ea-853a-4184f991a45f.png){: .mx-auto.d-block :}

### SMOTE

Apart from these random sampling with replacement, another over-sampling method is the *Synthetic Minority Oversampling Technique (SMOTE)*, which generates, as the named proposes, synthetic samples. 

It works creating synthetic samples from the minor class instead of creating copies as before. It selects ta minority class instance a at random and finds its k nearest minority class neighbors. The synthetic instance is then created by choosing one of the k nearest neighbors b at random and connecting a and b to form a line segment in the feature space. The synthetic instances are generated as a convex combination of the two chosen instances a and b [[1]](https://www.wiley.com/en-us/Imbalanced+Learning%3A+Foundations%2C+Algorithms%2C+and+Applications-p-9781118074626), [[7]](https://machinelearningmastery.com/tactics-to-combat-imbalanced-classes-in-your-machine-learning-dataset/), [[8]](https://www.datasciencecentral.com/profiles/blogs/handling-imbalanced-data-sets-in-supervised-learning-using-family#:~:text=The%20key%20difference%20between%20ADASYN,to%20compensate%20for%20the%20skewed), [[9]](https://imbalanced-learn.org/stable/over_sampling.html#a-practical-guide).

PS: SMOTE works in theory only with **numerical data**.

```javascript
from imblearn.over_sampling import SMOTE

X_num = np.asarray(X['Age']).reshape(-1,1)
smote = SMOTE(sampling_strategy='minority', random_state=42)
X_smote, y_smote = smote.fit_resample(X_num, y)
```

SMOTE increased the number os samples from 989 to 1704, with 852 survivals and 852 deaths. Ahead we see the new "Age" distribution:

![smote](https://user-images.githubusercontent.com/63553829/93004479-c703e780-f51d-11ea-9b99-16390c2715ae.png){: .mx-auto.d-block :}

## Borderline-SMOTE

In Borderline-SMOTE only borderline instances are considered to be SMOTEd, where borderline instances are defined as instances that are  misclassified by knn, generating only synthetic that are *difficult* to classify [[1]](https://www.wiley.com/en-us/Imbalanced+Learning%3A+Foundations%2C+Algorithms%2C+and+Applications-p-9781118074626), [[4]](https://machinelearningmastery.com/data-sampling-methods-for-imbalanced-classification/).

```javascript
from imblearn.over_sampling import BorderlineSMOTE

bord_smote = BorderlineSMOTE(sampling_strategy='minority', random_state=42)
X_bsmote, y_bsmote = bord_smote.fit_resample(X_num, y)
```

Borderline-SMOTE increased the number os samples from 989 to 1704, with 852 survivals and 852 deaths. Ahead we see again the new "Age" distribution:

![bsmote](https://user-images.githubusercontent.com/63553829/93004522-2104ad00-f51e-11ea-92fc-c5f1e64d05c4.png){: .mx-auto.d-block :}


## Borderline with SVM

Variant of SMOTE algorithm which use an Support Vector Machine algorithm to detect sample to use for generating new synthetic samples [[10]](https://imbalanced-learn.org/stable/generated/imblearn.over_sampling.SVMSMOTE.html).

As we can check above, Borderline-SMOTE with SVM does not result in a half-half number of samples.

```javascript
from imblearn.over_sampling import SVMSMOTE

bord_svm_smote = SVMSMOTE(sampling_strategy='minority', random_state=42)
X_bssmote, y_bssmote = bord_svm_smote.fit_resample(X_num, y)
```

Borderline-SMOTE with SVM increased the number os samples from 989 to 1704, with 852 survivals and 852 deaths. Ahead we see again the new "Age" distribution:

![svmsmote](https://user-images.githubusercontent.com/63553829/93004569-89538e80-f51e-11ea-9b8b-614c3da0176f.png){: .mx-auto.d-block :}

## SMOTENC

SMOTENC is a SMOTE variant for Nominal and Continuous, where one have a dataset with continuous and categorical features [[11]](https://imbalanced-learn.readthedocs.io/en/stable/generated/imblearn.over_sampling.SMOTENC.html#imblearn.over_sampling.SMOTENC). So here, added to "Age", we'll use binary categorical "Sex" predictor as well.

```javascript
from imblearn.over_sampling import SMOTENC

X_cat = X[['Sex','Age']]
smotenc = SMOTENC(sampling_strategy='minority', categorical_features=[X_cat.dtypes==object])
X_smotenc, y_smotenc = smotenc.fit_resample(X_cat, y)
```

SMOTENC increased the number os samples from 989 to 1704, with 852 survivals and 852 deaths. Ahead we see again the new "Age" distribution, as well as "Sex" distribution, which decreased in values:

![smotenc](https://user-images.githubusercontent.com/63553829/93004604-d6cffb80-f51e-11ea-9498-6a6d8915f2e8.png){: .mx-auto.d-block :}














