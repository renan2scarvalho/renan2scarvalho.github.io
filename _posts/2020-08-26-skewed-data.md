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

Real-world data can, and usually is, *messy*. **Skewed distribution** happens all the time, hence we need to work with this prior to modeling. A distribution
that is symmetric or nearly so is often easier to handle and interpret than a skewed distribution. Therefore, **transformations** are applied for many reasons, 
such as **reduce skewness**.

### But why is that?

More specifically, when removing skewness, transformations are attempting to make the dataset follow the *Gaussian distribution*.
The reason is simply that if the dataset can be transformed to be *statistically* close enough to a *normmal* or *Gaussian* dataset, then the largest set of
tools possible are available to them to use. Tests such as the ANOVA, t-test, F-test, and many others depend on the data having constant variance (σ²)
or follow a normal distribution.

As is know from statistics, **tails** (or in other words skewed distributions) may act as **outliers** for the statistical model, 
and *several models use polynomial calculations on the predictor data* i.e. labels, such as most linear models, neural networks, and SVM. 
Thus, in those cases, a *skewed label* distribution could have a **negative effect** over the models, since tails of distributions can dominate 
the underlying calculations, even though some models are robust to outliers (e.g. Tree-based), limiting the number of models that could be 
chosen [[1]](https://towardsdatascience.com/skewed-data-a-problem-to-your-statistical-model-9a6b5bb74e37).

So now let's begin!

## Hands-on!

The dataset used here is small sample of *imdb movies*. The primary steps here were to *drop missing values* (which are also a critical theme for data
cleansing), and *select numerical variables* (int64 and float64). The image ahead shows the dataframe partially, which has 3756 samples and 16 predictors.

```javascript
df.shape
(3756, 16)
```

![df](https://user-images.githubusercontent.com/63553829/91345851-64d58380-e7b6-11ea-9670-ecb355ed8f80.png){: .mx-auto.d-block :}

As a general rule of thumb, we can consider that:
- if skewness < -1 && skewness > 1: distribution is **highly skewed**;
- if -1 < skewness < -0.5 || 0.5 < skewness < 1: distribution is **moderately skewed**;
- if -0.5 < skeweness < 0.5: distribution is **approximately symmetric**.

Calculating the skewness for this data by using the **Fischer-Perarson standardized moment coefficient** [[2]](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.skew.html),[[3]](https://medium.com/@ODSC/transforming-skewed-data-for-machine-learning-90e6cc364b0), we see that only the predictor *"imdb_score"* is moderately skewed, which leaves us with a lot of work from now on!

```javascript
skewness = df.skew().sort_values(ascending=False)
skewness = pd.DataFrame(skewness, columns=['skewness'])
```

![sk](https://user-images.githubusercontent.com/63553829/91346892-dfeb6980-e7b7-11ea-9775-0b9c8c52dfbe.png){: .mx-auto.d-block :}

We can see the skewness from the histograms ahead for four predictors ("budget", "duration", "imdb_score", "title_year"), which are extremeley left
skewed, left skewed, moderately skewed, and  right skewed, respectively:

![hist](https://user-images.githubusercontent.com/63553829/91347239-5ee0a200-e7b8-11ea-87c5-04cd529c779c.png){: .mx-auto.d-block :}


### 1. Square Root Transformation

Use if:
- data have a **positive (right) skew***;
- data may be **counts or frequencies**;
- data have many **zeros or extremely small values**;
- data may have a **physical (power) component** (e.g. area  vs. length).

```javascript
srt = df[['budget','gross']]
srt = srt**(.5)
srt_sk = srt.skew()
srt_sk = pd.DataFrame(srt_sk, columns=['square root'])
sk = skewness.reset_index().merge(srt_sk.reset_index()).set_index('index')
```

Applying this transformation in two predictors that have positive skew: "budget" (largest skew) and "gross", we're able to **reduce skew** for both (in the case of "budget", from 44 to 7.5):

![sqrt](https://user-images.githubusercontent.com/63553829/91363184-72990200-e7d2-11ea-8fa5-795f5a89675d.png){: .mx-auto.d-block :}

![sqrt2](https://user-images.githubusercontent.com/63553829/91363226-8e040d00-e7d2-11ea-9b9a-e668b376a93c.png){: .mx-auto.d-block :}


### 2. Reciprocal transformation

Use if:
- data have a **positive (right) skew**;
- data may have been **originally derived by division**, or **represents ratio**;
 
PS: data must be positive, and not close to zero!

```javascript
invert = df[['aspect_ratio','duration']]
invert = 1/invert
invert1 = 1/(invert+1)
invert_sk = invert.skew()
invert1_sk = invert1.skew()
skew = pd.DataFrame(invert_sk, columns=['1/x'])
skew['1/(x+1)'] = invert1_sk
sk = skewness.reset_index().merge(skew.reset_index()).set_index('index')
```

Applying the transformation to "aspect_ratio" and "duration", both of which has a positive skew, we're able to **reduce skew** from 16 to -0.5, and from 2.4 to -0.24. One interesting thing here is that, if we add one to the values, the skew then becomes negative, with a better symmetry though:

![rec](https://user-images.githubusercontent.com/63553829/91363527-31edb880-e7d3-11ea-82ad-cdfd72060528.png){: .mx-auto.d-block :}

![rec2](https://user-images.githubusercontent.com/63553829/91363558-429e2e80-e7d3-11ea-9268-62723f5b833d.png){: .mx-auto.d-block :}


### 3. Logarithmic transformation

Use if:
- data have a **positive (right) skew**;
- you suspect an **exponential component** in the data;
- data might be best classified by **orders-of-magnitude** (e.g. 1,10,100);
- **cumulative main effects** are **multiplicative**, rather than additive.

PS: data must be positive, and the base of the logarithm is arbitrary (common bases are 10 and 2).

```javascript
ln = df[['budget','num_user_for_reviews']]
ln = np.log(ln)
ln1 = np.log(ln+1)
ln_sk = ln.skew()
ln1_sk = ln1.skew()
skew = pd.DataFrame(ln_sk, columns=['ln(x)'])
skew['ln(x+1)'] = ln1_sk
sk = skewness.reset_index().merge(skew.reset_index()).set_index('index')
```

Applying ln transformations on the predictiors "budget" and "num_user_for_reviews", and , we could **decrease skew** from 44 to -1.3, 3.8 to -0.16, respectively. So, in this case, the ln **transformation positively affects** skew:

![log](https://user-images.githubusercontent.com/63553829/91363831-e687da00-e7d3-11ea-989f-a1eff13b7e39.png){: .mx-auto.d-block :}

![log2](https://user-images.githubusercontent.com/63553829/91363863-f30c3280-e7d3-11ea-963a-45c6e37694b0.png){: .mx-auto.d-block :}

PS: log10 and log2, but they haven't shown better results, so they're kept off the post (they are in the Jupyter notebook though).


### 4. Exponential transformation

Use if:
- data have a **negative (left) skew**;
- you suspect an **underlying logarithmic trend** in the data (e.g. decay, survival, etc.);

```javascript
exp = df[['imdb_score']]
exp = np.exp(exp)
exp2 = 2**exp
exp_sk = exp.skew()
exp2_sk = exp2.skew()
skew = pd.DataFrame(exp_sk, columns=['exp(x)'])
skew['2^x'] = exp2_sk
sk = skewness.reset_index().merge(skew.reset_index()).set_index('index')
```

In this data, none of predicts seems to have an logarithmic trend. But applying the **transformation** in the "imdb_score" predictor we can see that it **negatively affects** skew:

![exp](https://user-images.githubusercontent.com/63553829/91364291-cad10380-e7d4-11ea-8e8a-7d624a766349.png){: .mx-auto.d-block :}


### 5. Power transformation 
Use if:

- Data have **negative (left) skew**.
- Data may have a **physical (power) component**, such as area vs. length.

```javascript
power2 = df[['imdb_score','title_year']]
power2 = power2**2
power3 = power2**3
p2_sk = power2.skew()
p3_sk = power3.skew()
skew = pd.DataFrame(p2_sk, columns=['x**2'])
skew['x**3'] = p3_sk
sk = skewness.reset_index().merge(skew.reset_index()).set_index('index')
```

After applying the power transformation to the "imdb_score" and "title_year" predictors, the **skew reduced** from -0.7 to -0.1, and from -2 to -1.85. In this case, the power transformation was beneficial to both predictors, and in the case of "title_year", an increase in the power could **reduce even more the skew** (the predictor is totally linear):

![pwr](https://user-images.githubusercontent.com/63553829/91364404-04097380-e7d5-11ea-96fc-43cb51eaf44f.png){: .mx-auto.d-block :}

![pwr2](https://user-images.githubusercontent.com/63553829/91364433-11266280-e7d5-11ea-93b0-945aec24ceaa.png){: .mx-auto.d-block :}


### 6. Arcsine transformation
Use if:

- Data are a **proportion** ranging between 0.0 - 1.0, or **percentage** from 0 - 100.
- **Most** data points are **between 0.2 - 0.8**, or **between 20 and 80** for **percentages**.

Since we do not have any kind of data as this here, we'll not approach *arcsine transformation*.


## Other transformations for skewed data
Now were we'll apply some more complex **power transformations** to create monotonic transformations of data using power functions, aiming to make date more *normal distribution-like* [[6]](https://en.wikipedia.org/wiki/Power_transform).

The power transformations is definde as a **continuously varying function**, with respect to a parameter λ, in a piece-wise function form that makes it continuous at the point of singularity (i.e. λ=0).


### 7. Box-Cox transformation
The Box-Cox procedure uses maximum likelihood estimation to estimate a transformation parameter λ in the equation [[7]](https://www.ime.usp.br/~abe/lista/pdfm9cJKUmFZp.pdf),[[8]](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.boxcox.html):

![eq](https://user-images.githubusercontent.com/63553829/91365133-d02f4d80-e7d6-11ea-9f79-910b77283dcf.png){: .max-auto.d-block :}

```javascript
from scipy.stats import boxcox as bc

bc_df = df.replace(0, np.nan).dropna(axis=1) # only values > 0
skew_bc = pd.DataFrame(bc_df.skew(), columns=['skewness'])

boxcox = []
for i in range(0, bc_df.shape[1]): 
    boxcox.append(bc(bc_df.iloc[:,i])[1])

skew_bc['box-cox'] = boxcox
skew_bc
```

As we can see, skewness reduced a lot for most of the predictors. However, some variables cannot be transformed to normal distributed-like, for several different reasons (e.g. imbalanced categorical classes).

![bc](https://user-images.githubusercontent.com/63553829/91365396-58155780-e7d7-11ea-97fb-15b77a9b2dbc.png){: .mx-auto.d-block :}

It is importante to notice, though, that the Box-Cox procedure can only be applied to data that is **strictly positive!** To overcome this issue, the Yeo-Johnson procedure is addressed ahead.


### 8. Yeo-Johnson transformation
Yeo-Johnson transformation comes to **address the non-zero and non-negative values** issue of the Box-Cox transformation. Works almost the same way, but when estimating the transformation parameter, it founds the value of λ that minimizes the [Kullback-Leibler distance](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) between the normal and transformed distribution [[7]](https://www.ime.usp.br/~abe/lista/pdfm9cJKUmFZp.pdf), [[9]](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.yeojohnson.html):

![eq2](https://user-images.githubusercontent.com/63553829/91365495-a7f41e80-e7d7-11ea-826a-00aaefe196f5.png){: .mx-auto.d-block :}

```javascript
from scipy.stats import yeojohnson as yeo

skew_yeo = pd.DataFrame(df.skew(), columns=['no transformation'])

yeo_johnson = []
for i in range(0, df.shape[1]): 
    yeo_johnson.append(yeo(df.iloc[:,i])[1])

skew_yeo['yeo-johnson'] = yeo_johnson
skew_yeo
```

Here, the results were *similar* to the Box-Cox transformation. Nevertheless, Yeo-Johnson transformation has the big advantage of approach all possible values, in contrast to Box-Cox.

![yeo](https://user-images.githubusercontent.com/63553829/91365602-e8ec3300-e7d7-11ea-9733-eedec1690f03.png){: .mx-auto.d-block :}


### Highlights
As we can see, for most of the cases the transformations had a fine result, with Yeo-Johnson transformation being slightly better than Box-Cox for the same keys. It's important to notice that **both transformations are unsupervised**, and while a transformation might **improve** the distribution, it has **no guarantee** that will **improve the model**.

```javascript
comparison = skew_bc.reset_index().merge(skew_yeo, how='left').set_index('index')
```

![comp](https://user-images.githubusercontent.com/63553829/91365767-4c766080-e7d8-11ea-8af7-7d157c7b093d.png)
