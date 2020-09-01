---
layout: post
title: Extraction, Transformation, and Loading with Pentaho
subtitle: BI Fundamentals and Tools - Part 2
thumbnail-img: https://pentahobrazil.files.wordpress.com/2013/12/pentaho-logo.png
#cover-img: 
#share-img: 
gh-repo: renan2scarvalho/Bootcamp_BI/Módulo 3/Workshop BI
gh-badge: [star, fork, follow]
tags: [BI, ETL, pentaho]
comments: true
---

### So let's continue...

[Part 1](https://renan2scarvalho.github.io/2020-08-31-ETL-Pentaho-Pt1/) of this post covered the **extraction** step. 
Just to recap, we're performing an ETL process for a ficticious technology (hardware and software) company, going from Transaction to a Dimensional Model.
Now let's keep it moving...

### 3. Transformation for Adequacy

Here we'll apply the bussiness vision (**always!**) to implement the dimensional model. Only by applying the 5W2H is already possible to have a geographic, financial, behavioral, and temporal behavior:

- *Who* are the customers?
- *What* they buy?
- *Where* they buy?
- *When* they buy?
- *How much* they pay?
- *How* much they have available to buy?

Now we apply the necessary transformations for each dimension presented above.

#### 1. Dimension Product

We begin with the Dimension Product, since it's one of the simplest transformations, which consists in apply an *input* table with the same MySQL *Staging Area* connection, and *sort* rows accordingly with the PK *PRODUCT_ID*:

![dim_prod](https://user-images.githubusercontent.com/63553829/91759318-d98a3280-eba7-11ea-91d2-725d97dbb5c5.png){: .mx-auto.d-block :}

After this simple **transformation**, we can **load** the table in the DW. But for that, we *first* need to create the DW:

```javascript
CREATE DATABASE dw_workshopbi;
```

The we can call the *dimension* block, create another connection, now with the DW (remember to **always test** the connection):

![dim_prod2](https://user-images.githubusercontent.com/63553829/91759735-88c70980-eba8-11ea-9620-297b40e9e100.png){: .mx-auto.d-block :}

Now we'll configure the dimension, beginning by the name *Target table*, selection PK *PRODUCT_ID* in the Tab *Keys*, and *Get Fields* in the Tab *Fields*. We must create a technical SK, which we'll call *sk_product* with auto-increment and a version:

![dim_prod3](https://user-images.githubusercontent.com/63553829/91760152-389c7700-eba9-11ea-87e2-472c2fb9b5a4.png){: .mx-auto.d-block :}

We must add an alternative date for the SK. But to complete this step, we must create the table for the dimension in MySQL. again, we can query directly in PDI to create it:

![dim_prod4](https://user-images.githubusercontent.com/63553829/91760605-04758600-ebaa-11ea-8a34-86172ffa5ada.png){: .mx-auto.d-block :}

After all these steps, we can now run our dimension pipeline, and if we are successiful, the following message will appear:

![dim_prod5](https://user-images.githubusercontent.com/63553829/91760779-54544d00-ebaa-11ea-95f5-9014f57d6e90.png){: .mx-auto.d-block :}

Done! We just made our first **transformation and load** into the DW! 

{: .box-error}This final step will be presented here only once, since it's common for all other dimensions, so pay attention and be careful!


#### 2. Dimension Promotion

This Dimension has the exactly same pipeline (remember to *sort* with PK) as the Dimension Product, so it will not be discussed with greater scrutiny:

![dim_prom](https://user-images.githubusercontent.com/63553829/91762478-c8432500-ebab-11ea-9536-be89ed7c35cc.png){: .mx-auto.d-block :}


#### 3. Dimension Customer

Dimension Customer begins *sorting* the wors by the PK *CONSUMER_ID*, followed by *string operations*, where we lower the attribute *CUST_EMAIL*:

![dim_cons](https://user-images.githubusercontent.com/63553829/91763740-6a630d00-ebac-11ea-98dd-4e73181525eb.png){: .mx-auto.d-block :}

Next, we replace null values with the box *If field value is null*:

![dim_cons2](https://user-images.githubusercontent.com/63553829/91763863-9e3e3280-ebac-11ea-9f5f-f837ecad1af4.png){: .mx-auto.d-block :}

We must select the necessary attributes accordingly with the business vision:

![dim_cons3](https://user-images.githubusercontent.com/63553829/91764100-16a4f380-ebad-11ea-931f-ca737ff0294c.png){: .mx-auto.d-block :}

After all these **transformations**, we can **load** the Dimension in the DW, again using the MySQL connection.


#### 4. Dimension SalesRep

For this dimension, we begin filtering the rows. If we take a closer look, the first row has an *ID* of zero, so we must filter that out. So we apply the box *Filter rows* in a way that when *EMPLOYEE_ID = 0* it goes to a *Dummy* box, which does nothing, and for the rest, it continues the pipeline:

![dim_sr](https://user-images.githubusercontent.com/63553829/91764602-f9bcf000-ebad-11ea-8d1f-75f1221a28f5.png){: .mx-auto.d-block :}

After, since all *ID_DEPARTMENT* values are 80, we apply this *most frequent* replacement for that attribute, and *sort* rows by *EMPLOYEE_ID*:

![dim_sr](https://user-images.githubusercontent.com/63553829/91764982-9f705f00-ebae-11ea-96b7-d2ee37a0b5c0.png){: .mx-auto.d-block :}

After that, we can *load* data into the DW. Done! We have completed another **transformation and load** step. We're almost done, but we must create only one more Dimension, which is essential for any dimensional model: *Dimension Date*. So let's do it!


#### 5. Dimension Date

*Finally!* Our **last** Dimension Date is *sine qua non* for every dimensional model. In this case, we'll create this Dimension from scratch. So we begin with a *Generate rows* box, creating a baseline for all dates, which begins in 01/01/1990 (dd/MM/YYYY), and ends 100,000 days after the beginning:

![dim_data](https://user-images.githubusercontent.com/63553829/91766247-a7c99980-ebb0-11ea-8cd1-84b1e105eb85.png){: .mx-auto.d-block :}

Next, we *add a sequence* of days, since the baseline only has equal dates:

![dim_data2](https://user-images.githubusercontent.com/63553829/91766485-0727a980-ebb1-11ea-87f3-bcb3876387f0.png){: .mx-auto.d-block :}

In this third step, we can use a *calculator* to compute the date attributes. Here, one must be careful with the *Field A* and *Field B*, as well as  *Value Type* and *Conversion Mask*:

![dim_data3](https://user-images.githubusercontent.com/63553829/91766606-39390b80-ebb1-11ea-98fb-b383dae3cb1c.png){: .mx-auto.d-block :}

Here we can *map values*, to generate string dates with quarters and semesters, as well as month and days of the week with names:

![dim_data4](https://user-images.githubusercontent.com/63553829/91767190-26730680-ebb2-11ea-9a63-bfe1fc150f15.png){: .mx-auto.d-block :}

Now we just have to remove the fields created in the beginning, and **load** data into the DW:

![dim_data5](https://user-images.githubusercontent.com/63553829/91767300-4dc9d380-ebb2-11ea-9bbf-a9a7e9b09b48.png){: .mx-auto.d-block :}

**Finally!** All Dimensions are made and load into the DW! Our *final ETL step: Fact Table Sales*.


#### 6. Fact Sales

Now, after calling the Sales table from the *staging area*, we use a *calculator* to compute *total sales*, and create a new date attribute so we can standardize the dates from Sales table (YYYY/MM/dd) and the Dimensions (dd/MM/YYYY) throough a *Conversion mask*:

![fato](https://user-images.githubusercontent.com/63553829/91771287-1d396800-ebb9-11ea-94a2-d0e09df96a9d.png){: .mx-auto.d-block :}

On the next step, we'll performe a *lookup* over the Dimensions, using the DW connection on MySQL. We apply the PKs and FKs as connection between the tables for the lookup, and return the SKs:

![fato2](https://user-images.githubusercontent.com/63553829/91770591-d303b700-ebb7-11ea-9435-b11752e4f5d1.png){: .mx-auto.d-block :}
![fato3](https://user-images.githubusercontent.com/63553829/91770488-9e8ffb00-ebb7-11ea-8953-4664468d0c10.png){: .mx-auto.d-block :}
![fato4](https://user-images.githubusercontent.com/63553829/91770681-f9c1ed80-ebb7-11ea-97eb-84d3e255c71e.png){: .mx-auto.d-block :}

We must replace SKs null values:

![fato5](https://user-images.githubusercontent.com/63553829/91771366-3f32ea80-ebb9-11ea-803f-76389a1c8adf.png){: .mx-auto.d-block :}

And remove the *IDs* from the fact, since the SKs serves this purpose

![fato6](https://user-images.githubusercontent.com/63553829/91771510-7b664b00-ebb9-11ea-999a-de554c659c65.png){: .mx-auto.d-block :}

All right! We're almost done, just have to **load** the fact into the DW, we've finished. So just create the table through PDI SQL query to ease the process, and *it's done!*
After the following query inside the MySQL Workbench, we can check the success of this ETL process:

![mysql](https://user-images.githubusercontent.com/63553829/91771809-021b2800-ebba-11ea-8f23-6291d324655d.png){: .mx-auto.d-block :}

We can notice that this **ETL** process gave us some work, have lots of details, but isn't complicated. Just *need attention!* Also, here we applied extraction acconrdingly to the files extention, but the best practice would performe extraction, transformation, and loading of each input *individually*. Added to that, we could automate the pipeline through the Pentaho *Jobs*, scheduling the ETLs, sendind emails of success. Even though this is quite simple, it's out of the boundaries of this post.



