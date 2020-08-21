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

## First things first

Firstly, let me preset you the dimensional model, which consists of 4 tables. It's can be noticed that, in order to ease the mode,
data dimension was excluded. The tables with columns are:

| Transactions | Customers | Products | Stores |
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


## Now hands-on!

The technical part of the interview was composed by 5 questions, which approached mainly data quick queries to generate quick insights. In this case, I opted to use Python and the Pandas library. Ahead I show the first five lines of each dataframe, as well as its shape.

![image](https://user-images.githubusercontent.com/63553829/90934480-c0c79300-e3d7-11ea-8fc7-6376dea5d61c.png)

![image](https://user-images.githubusercontent.com/63553829/90934543-db017100-e3d7-11ea-858e-c1070f05e5bd.png)

![image](https://user-images.githubusercontent.com/63553829/90934556-e18fe880-e3d7-11ea-97d7-15e259dd102e.png)

![image](https://user-images.githubusercontent.com/63553829/90934597-f0769b00-e3d7-11ea-9c50-21d1e23130b6.png)

{% highlight javascript lineos %}
stores.shape, customer.shape, product.shape, transactions.shape
((10, 3), (55, 3), (20, 4), (200, 8))
{% endhighlight %}

So let's get to the **questions**. 
1. The first question consisted in sorting (ascending) the table "customer" by customer_id and removing duplicates. This is a simple question, where we apply pandas *sort_values* with feature to sort by, and apply the *remove_duplicates* with *ignote_index* so the indexes goes from 0 to n-1. The code is:

{% highlight javascript lineos %}
customer = customer.sort_values(by='customer_id', ascending=True).drop_duplicates(ignore_index=True)
customer.shape
(50, 3)
{% endhighlight %}

![image](https://user-images.githubusercontent.com/63553829/90934963-9aeebe00-e3d8-11ea-90fe-1c6da8e3ddbb.png)

Quite easy right?! I put 5 duplicates specially for this exercise. So let's go to the next

2. The second question was to create a table "transaction_cube" merging all tables. So this, although simple, gives some work. Here we use pandas *merge*, which has by default an inner join (if you don't recall what join is, check [here](https://en.wikipedia.org/wiki/Join_(SQL))) to merge the fact table transactions
SKs and all other PKs of the dimension tables. Here's the code:

{% highlight javascript lineos %}
transactions_stores = transactions.merge(stores, on='store_id') 
transactions_customer = transactions_stores.merge(customer, on='customer_id') 
transaction_cube = transactions_customer.merge(product, on='product_id')
transaction_cube.shape
(153, 15)
{% endhighlights %}

![image](https://user-images.githubusercontent.com/63553829/90935672-1dc44880-e3da-11ea-8af9-a5c304f9ac8f.png)







