---
layout: post
title: My first Data Science Interview
subtitle: Working with Pandas
gh-repo: renan2scarvalho/First_DS_Interview
gh-badge: [star, fork, follow]
tags: [data science, python, pandas]
comments: true
---

This is my first post ever, and I'll start it talking about my first Data Science job interview. It happened a couple months ago, and it didn't went 
so well, unfortunately. In this post I approach some pandas questions that happened during the interview, using ficticious samples created randomly by 
myself in order to ease the understanding, even though the questions are in majority simple.
So let's begin!

** First things first**

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
