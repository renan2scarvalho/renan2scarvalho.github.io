---
layout: post
title: My first Data Science Interview
subtitle: Working with Python and Pandas
gh-repo: renan2scarvalho/First_DS_Interview
gh-badge: [star, fork, follow]
tags: [data science, python, pandas]
comments: true
---

This is my first post ever, and I'll start it talking about my first Data Science job interview. It happened a couple months ago, and it didn't go 
so well, unfortunately. In this post I approach some questions that happened during the interview, using ficticious samples created randomly by 
myself in order to ease the understanding, even though the questions are in majority simple.
So let's begin!

## But first things first...

Let me present you the dimensional model, which consists of 4 tables. It can be noticed that, in order to ease the model in the interview,
data dimension was excluded (and added in "transaction" table as FK). The tables with columns are:

| Transaction | Customer | Product | Store |
|:----|:----|:----|:----|
| ticket_id | customer_id | product_id | store_id |
| customer_id | age | product_name | store_name |
| store_id | gender | supplier | state |
| product_id | | department | |
| date_id |  | | |
| unit_price | | | |
| quantity | | | |
| sales | | | |

The structure of this dimensional model is as follows:

![dim_mdl](https://user-images.githubusercontent.com/63553829/90960048-4c433180-e475-11ea-84be-13c8b324e448.png){: .mx-auto.d-block :}

## Now hands-on!

The technical part of the interview was composed by 5 questions (which I adapted here), and approached mainly data quick queries to generate quick insights. In this case, I opted to use Python and the Pandas library. I show the first five lines of each dataframe below, as well as its shape.

![transaction](https://user-images.githubusercontent.com/63553829/90934480-c0c79300-e3d7-11ea-8fc7-6376dea5d61c.png){: .mx-auto.d-block :}

![customers](https://user-images.githubusercontent.com/63553829/90934543-db017100-e3d7-11ea-858e-c1070f05e5bd.png){: .mx-auto.d-block :}

![products](https://user-images.githubusercontent.com/63553829/90934556-e18fe880-e3d7-11ea-97d7-15e259dd102e.png){: .mx-auto.d-block :}

![stores](https://user-images.githubusercontent.com/63553829/90934597-f0769b00-e3d7-11ea-9c50-21d1e23130b6.png){: .mx-auto.d-block :}

```javascript
stores.shape, customer.shape, product.shape, transactions.shape
((10, 3), (55, 3), (20, 4), (200, 8))
```

So let's get to the **questions**. 
1 - The first question consisted in sorting (ascending) the table "customer" by customer_id and removing duplicates. This is a simple question, where we apply pandas *sort_values* with feature to sort by, and apply the *remove_duplicates* with *ignote_index* so the indexes goes from 0 to n-1. The code is:

```javascript
customer = customer.sort_values(by='customer_id', ascending=True).drop_duplicates(ignore_index=True)
customer.shape
(50, 3)
```

![sort](https://user-images.githubusercontent.com/63553829/90934963-9aeebe00-e3d8-11ea-90fe-1c6da8e3ddbb.png){: .mx-auto.d-block :}

Quite easy right?! I put 5 duplicates specially for this exercise. So let's go to the next


2 - The second question was to create a table "transaction_cube" merging all tables. So this, although simple, can be tricky. Here we use pandas *merge*, which has by default an inner join (if you don't recall what join is, check [here](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html) to merge the fact table transactions
SKs and all other PKs of the dimension tables. Here's the code:

```javasc
transactions_stores = transactions.merge(stores, on='store_id') 
transactions_customer = transactions_stores.merge(customer, on='customer_id') 
transaction_cube = transactions_customer.merge(product, on='product_id')
transaction_cube.shape
(153, 15)
```

![cube](https://user-images.githubusercontent.com/63553829/90935672-1dc44880-e3da-11ea-8af9-a5c304f9ac8f.png){: .mx-auto.d-block :}


3 - The thrird question was to create a table "customer_ids" with customers that existed in the "transactions" table, but not in the "customer" table. So we again use pandas *merge* with an indicator *i* and an *outer* join. Here this results in a table with *both* for inner keys, and *left_only* for keys present only in "transaction" table, then we use this indicator as a selector, and drop the indicator column afterwards. Here's the code:

```javascript
customer_ids = transactions.merge(customer, indicator='i', how='outer').query('i == "left_only"').drop('i',axis=1)
```

![cust_ids](https://user-images.githubusercontent.com/63553829/90936928-aa700600-e3dc-11ea-8a4d-c14ebbe10a97.png){: .mx-auto.d-block :}


4 - The fourth question consisted in creating the table "customer_summary" with the following variables: customer_id, department, total_sales, total_quantity, average_ticket, last_visit. So for that, we use pandas *groupby* to group by *customer_id* and apply the necessary measure for each of the variables e.g. count(), sum() or mean(). Here is necessary to transform the *date_id* to pandas *datetime*. The code is as follows:

```javascript
transaction_cube['date_id'] = pd.to_datetime(transaction_cube['date_id']) # date_id as datetime
customer_id = sorted(transaction_cube['customer_id'].unique())
customer_summary = pd.DataFrame(customer_id, columns=['customer_id'])
customer_summary['department'] = transaction_cube.groupby(['customer_id'])['department'].count().to_list()
customer_summary['total_sales'] = transaction_cube.groupby(['customer_id'])['sales'].sum().to_list()
customer_summary['total_quantity'] = transaction_cube.groupby(['customer_id'])['quantity'].sum().to_list()
customer_summary['average_ticket'] = transaction_cube.groupby(['customer_id'])['sales'].mean().to_list()
customer_summary['last_visit'] = transaction_cube.groupby(['customer_id'])['date_id'].max().to_list()
customer_summary.set_index('customer_id', inplace=True)
```

![cust_summ](https://user-images.githubusercontent.com/63553829/90937527-5154a200-e3dd-11ea-8b3c-dddbb734cc80.png){: .mx-auto.d-block :}


5 - The fifth and last question was to calculate the table "customer_metrics" with the following variables: customer_id, department, total_sales, total_quantity, product_name (most sold product per customer), and price_median. Here some variables were already calculated, so we can use table "customer_summary", as we have been doing in a cascade since the beginning. For the *product_name*, I had to first *groupby* and *count()*, *reset_index*, *sort_values* by *store_id* (in this case, as result from the previous step, the variable could be any), *groupby* again, and select the *first()* row. Here is the code:

```javascript
customer_metrics = customer_summary.copy()
gb = transaction_cube.groupby(['customer_id','product_name']).count(); gb.reset_index(inplace=True)
gb = gb.sort_values('store_id', ascending=False).groupby(['customer_id']).first()
customer_metrics['product_name'] = gb['product_name']
customer_metrics['price_median'] = transaction_cube.groupby(['customer_id'])['unit_price'].median()
```

![cust_met](https://user-images.githubusercontent.com/63553829/90938008-71d12c00-e3de-11ea-87da-0969817b99ab.png){: .mx-auto.d-block :}


## Now let's apply some machine learning!

Extending the context of the interview, let's quickly apply some machine learning in this sample to cluster the customers variables using the following features:

- monetary -> volume of sales per customer
- frequency -> how frequent are the customers
- recency -> are the customer a recent or old buyer

![hist](https://user-images.githubusercontent.com/63553829/90985027-694c3300-e54f-11ea-910f-7ce2f73f393d.png){: .mx-auto.d-block :}

For this, we'll use the *[K-means algorithm](https://en.wikipedia.org/wiki/K-means_clustering)*. It basically consists in partitionin *n* observations into *k* clusters in which each observation belongs to the cluster with the nearest mean, i.e. cluster centers centroid, minimizing the loss functino within-cluster variance, in this case the squared Eucliden distance. But before this, let's quicly apply the *[Hopkins statistic](https://en.wikipedia.org/wiki/Hopkins_statistic)*, which basically measures the cluster tendency, where values close to 1 indicates that the data is highly clustered. In our case, the *Hopkins statistic* resulted in *0.72*, which means that our data can be clustered relatively well.
The ML model isn't the aim of this post, so I'll pass through the pipeline quickly, with a few code lines. We begin it applying a [Standard Scaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) as a preprocessing, which works the mean and scaling to unit variance, and then applied the K-means algorithm with the number of clusters *k = 3* arbitrarily chosen (here we should use some metrics e.g. elbow curve to find *k*, but as pointed before, this analysis isn't the aim of this post).

```javascript
from sklearn.preprocessing import StandardScaler
from sklearn.cluster import KMeans

scaler = StandardScaler()
df_scaled = scaler.fit_transform(df)
```

![df](https://user-images.githubusercontent.com/63553829/90984086-44ed5800-e549-11ea-86ab-e86b60ced6cd.png) -> ![df2](https://user-images.githubusercontent.com/63553829/90984101-5898be80-e549-11ea-819c-76b20869f627.png)

Now let's see the result of this clustering procedure graphically. Here Cluster 0 is represented in blue, Cluster 1 in orange, and Cluster 2 in green:

![3d](https://user-images.githubusercontent.com/63553829/90984237-1ae86580-e54a-11ea-97b1-27cc0beec18e.png){: .mx-auto.d-block :}

To complete this analysis let's see the boxplots for the Clusters and the variables:
- monetary:
![monetary](https://user-images.githubusercontent.com/63553829/90984304-6a2e9600-e54a-11ea-9704-9dfe2c6d98b0.png){: .mx-auto.d-block :}

- frequency:
![frequency](https://user-images.githubusercontent.com/63553829/90984317-803c5680-e54a-11ea-9274-7e300c9a84f7.png){: .mx-auto.d-block :}

- recency:
![recency](https://user-images.githubusercontent.com/63553829/90984330-92b69000-e54a-11ea-9a71-935d92d7b6ae.png){: .mx-auto.d-block :}


From these bloxplots, some assumptions can be made:

- **Cluster 0:** customers that have a large range of average ticket (close to 0 up to 800), not so frequent but old buyers, and compose the majority of customers (22 of 46 = app. 50%);
- **Cluster 1:** customers with average ticket ranging from 100 up to 500, highly frequent and mostly old buyers (13 of 46 = app. 30%);
- **Cluster 2:** customers with a higher average ticket value i.e. more expensive products, but no so frequent and recent buyers (11 of 46 = app. 20%);

This analysis are quite interesting. From this quick inspection one could elaborate a marketing campaign focused on each Cluster, adding some other features e.g. age and gender, which can increase the possibility of success. For example, customers from Cluster 0 and Cluster 1 are old buyers, so they don't need some intense marketing camping, since they are already frequent buyers and, in case of Cluster 0 customers, with a high volume. Cluster 2, however, need a more intense and specific campaign, since they are relatively recent buyers with a high average ticket, which can mean they buy specific and expensive products. It's important to notice that these couple last lines are assumptions, and should be analyzed with a higher scrutiny.
