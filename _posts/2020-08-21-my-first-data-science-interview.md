---
layout: post
title: My first Data Science Interview
subtitle: Working with Python and Pandas
gh-repo: renan2scarvalho/First_DS_Interview
gh-badge: [star, fork, follow]
tags: [data science, python, pandas]
comments: true
---

This is my first post ever, and I'll start it talking about my first Data Science job interview. It happened a couple months ago, and it didn't went 
so well, unfortunately. In this post I approach some questions that happened during the interview, using ficticious samples created randomly by 
myself in order to ease the understanding, even though the questions are in majority simple.
So let's begin!

## But first things first...

Firstly, let me preset you the dimensional model, which consists of 4 tables. It's can be noticed that, in order to ease the model in the interview,
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

![dim_mdl](https://user-images.githubusercontent.com/63553829/90960048-4c433180-e475-11ea-84be-13c8b324e448.png)

## Now hands-on!

The technical part of the interview was composed by 5 questions (which I adapted here), and approached mainly data quick queries to generate quick insights. In this case, I opted to use Python and the Pandas library. Ahead I show the first five lines of each dataframe, as well as its shape.

![transaction](https://user-images.githubusercontent.com/63553829/90934480-c0c79300-e3d7-11ea-8fc7-6376dea5d61c.png)

![customers](https://user-images.githubusercontent.com/63553829/90934543-db017100-e3d7-11ea-858e-c1070f05e5bd.png)

![products](https://user-images.githubusercontent.com/63553829/90934556-e18fe880-e3d7-11ea-97d7-15e259dd102e.png)

![stores](https://user-images.githubusercontent.com/63553829/90934597-f0769b00-e3d7-11ea-9c50-21d1e23130b6.png)

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

![sort](https://user-images.githubusercontent.com/63553829/90934963-9aeebe00-e3d8-11ea-90fe-1c6da8e3ddbb.png)

Quite easy right?! I put 5 duplicates specially for this exercise. So let's go to the next


2 - The second question was to create a table "transaction_cube" merging all tables. So this, although simple, gives some work. Here we use pandas *merge*, which has by default an inner join (if you don't recall what join is, check [here](https://en.wikipedia.org/wiki/Join_(SQL))) to merge the fact table transactions
SKs and all other PKs of the dimension tables. Here's the code:

```javasc
transactions_stores = transactions.merge(stores, on='store_id') 
transactions_customer = transactions_stores.merge(customer, on='customer_id') 
transaction_cube = transactions_customer.merge(product, on='product_id')
transaction_cube.shape
(153, 15)
```

![cube](https://user-images.githubusercontent.com/63553829/90935672-1dc44880-e3da-11ea-8af9-a5c304f9ac8f.png)


3 - The thrid question was to create a table "customer_ids" with customers that existed in the "transactions" table, but not in the "customer" table. So we again use pandas *merge* with an indicator *i* and an *outer* join. Here this results in a table with *both* for inner keys, and *left_only* for keys present only in "transaction" table, then we use this indicator as a selector, and drop the indicator column afterwards. Here's the code:

```javascript
customer_ids = transactions.merge(customer, indicator='i', how='outer').query('i == "left_only"').drop('i',axis=1)
```

![cust_ids](https://user-images.githubusercontent.com/63553829/90936928-aa700600-e3dc-11ea-8a4d-c14ebbe10a97.png)


4 - The fourth question consisted in creating the table "customer_summary" with the following variables: customer_id, department, tota_sales, total_quantity, average_ticket, last_visit. So for that, we use pandas *groupby* to group by *customer_id* and apply the necessary measure for each of the variables e.g. count(), sum() or mean(). Here is necessary to transform the *date_id* to pandas *datetime*. The code is as follows:

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

![cust_summ](https://user-images.githubusercontent.com/63553829/90937527-5154a200-e3dd-11ea-8b3c-dddbb734cc80.png)


5 - The fifth and last question was to calculate the table "customer_metrics" with the following variables: customer_id, department, total_sales, total_quantity, product_name (most sold product per customer), and price_median. Here some variables were already calcualted, so we can use table "customer_summary", as we have been doing in a cascade since the beginning. For the *product_name*, I had to first *groupby* and *count()*, *reset_index*, *sort_values* by *store_id* (in this case, as result from the previous step, the variable could be any), *groupby* again, and select the *first()* row. Here is the code:

```javascript
customer_metrics = customer_summary.copy()
gb = transaction_cube.groupby(['customer_id','product_name']).count(); gb.reset_index(inplace=True)
gb = gb.sort_values('store_id', ascending=False).groupby(['customer_id']).first()
customer_metrics['product_name'] = gb['product_name']
customer_metrics['price_median'] = transaction_cube.groupby(['customer_id'])['unit_price'].median()
```

![cust_met](https://user-images.githubusercontent.com/63553829/90938008-71d12c00-e3de-11ea-87da-0969817b99ab.png)





