---
layout: post
title: Extraction, Transformation, and Loading with Pentaho
subtitle: BI Fundamentals and Tools - Part 1
thumbnail-img: https://pentahobrazil.files.wordpress.com/2013/12/pentaho-logo.png
#cover-img: 
#share-img: 
gh-repo: renan2scarvalho/Bootcamp_BI/Módulo 3/Workshop BI
gh-badge: [star, fork, follow]
tags: [BI, ETL, pentaho]
comments: true
---

The [Gartner Group](https://www.gartner.com/en/information-technology/glossary/business-intelligence-bi) describes Business Intelligence (BI)
as the application of a group of methodologies and technologies to improve the companies operations' efficiency and 
support decision makers to reach competitive advantages. Its architecture consists basically of three main components: 

- **Extraction, Transformation, and Loading (ETL):** extract data from operational databases, perform transformations to create valid
valid information for *a posteriori* analysis (e.g. *ad hoc* reports, OLAPs) in order to support decision makers, and finally store 
this in a warehouse;
- **Data Warehouse:** data repository that apply modeling (e.g.dimensional modeling), with integrated, standardized,
detailed, and historical, to support operational, tactical, and strategic decision making;
- **Presentation:** analyze and apply front-end tools to visualize data in a friendly way, to support decision making.

Nevertheless, ETL takes about 70% of the the total time to develop a BI application. So let's look what is the ETL process with more detail:

- **Extraction:** data is extract from Online Transaction Processing (OLTPs) with several different extentions (e.g. sql, csv, excel, json)
and is conducted to the staging area, which is basically a temporary area, where all data is standardized;
- **Transformation:** perform several adjusts, improving data quality, and consolidate data from one or more sources;
- **Load:** load the structured data into the presentation layer, following the dimensional model.

![ETL](https://miro.medium.com/max/480/1*3RT78P9QznDCf1cs4-8_9Q.jpeg){: .center}

Source: [[1]](https://medium.com/@aviralsrivastava/how-etl-designs-and-data-modeling-are-important-for-designing-a-data-warehouse-2b3cc3514d0e)

For this post, we'll use Pentaho Data Integration and MySQL Workbench to realize an ETL process as part of a workshop. So let's take a quick look 
over Pentaho.

## Pentaho Data Integration (PDI)

[Pentaho Data Integration](https://help.pentaho.com/Documentation/7.1/0D0/Pentaho_Data_Integration) (originally called "Kettle") is an **open source** 
software that provides the ETL capabilities
that facilitates the process of capturing, cleansing, and storing data using a uniform and consistent format that is accessible and relevant 
to end users and IoT technologies. Some common uses of Pentaho Data Integration include:

- Data migration between different databases and applications;
- Loading huge data sets into databases taking full advantage of cloud, clustered and massively parallel processing environments;
- Data Cleansing with steps ranging from very simple to very complex transformations;
- Data Integration including the ability to leverage real-time ETL as a data source for Pentaho Reporting;
- Data warehouse population with built-in support for slowly changing dimensions and surrogate key creation (as described above).

Now we now what the ETL process is about, and the tools to work with it, so let's get it done!

PS: this post (Part 1) will approach only the **extraction** step. The **transformation** and **load** will be on [Part 2](https://renan2scarvalho.github.io/2020-08-31-ETL-Pentaho-Pt2/).


## Hands-on!

The workshop aimed to demonstrate in a practical and objective way how to transform a transaction base model (OLTP) in a dimensional model (OLAP) for a Data Warehouse (DW). The project used four *csv* files of a ficticious technology company (hardware and software). The worksheets are as follows, and they can be found on [this](https://github.com/renan2scarvalho/Bootcamp_BI/tree/master/M%C3%B3dulo%203/Workshop%20BI) Git Rep:
- customer
- orders
- products
- promotions
- salesrep

This ETL procedure consisted in 5 steps:
1. Identify data sources and the resulting dimensional model;
2. Create a staging area and extract data;
3. Implement the dimensional model with fits the business model;
4. Structure the dimension and fact tables;
5. Organize the ETL pipeline.

Here we'll use, as commented above, two open source tools, *Pentaho* and *MySQL*, which allows us to have a high quality process with zero cost. So let's begin!


### 1. Transaction and Dimensional Models

The dataset consists in a classic retail scheme, with a list of products offered by the company. An order contains several items, and is attached to a sales representative, and can also be attached to a promotion.
The following scheme represents this transction model, where each Primary Key (PK) identifies one item, whereas Foreign Keys (FK) connects tables:

![trans_sch](https://user-images.githubusercontent.com/63553829/91753039-a8a50000-eb9d-11ea-9bd9-18bfd3425ccb.png){: .mx-auto.d-block :}

Now approaching the dimensional model, when in the DW, the PKs have another name: Surrogate Key (SK), which is basically an artificial key that exists to maintain the integrity of a dimension's information, since it has the characteristics of a PK. 
When a PK comes from a transaction base, is also called *Natural Key* or *Business Key*. The following star schema, proposed by [Kimball](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/star-schema-olap-cube/), will be the following:

![star_sch](https://user-images.githubusercontent.com/63553829/91753371-2cf78300-eb9e-11ea-9395-eb857fd85a3b.png){: .mx-auto.d-block :}


### 2. Staging Area

The Staging Area consists in creating a temporary area to extract tables in order to standardize them prior to transformation. Since now it's the time to do data treatment and join data, we can presume that this would demand a high data processing cost, and if it's made directly in the data origin it probably would affect the performance of the production systems, since the computational resource is high. Another reason is that in several cases is necessary to cross information existing in different data bases physically separated, and the staging area has the tables in the same place to perform this cross-information. So the staging area is nothing more than a relational data base which works as a repository for the extracted transaction informations so they can be treated. So, to begin, we must create our staging area in MySQL with the following code:

```javascript
CREATE DATABASE stg_workshopbi;
```

Now we can **extract** data inside the PDI. The best practice is to extract each dataset separately, but here we will perform the extraction of all databases with the same extension (*csv*) all at once to quick the process. We must add *input* boxes, as well as *output* boxes to the *MySQL Staging Area*:

![ext](https://user-images.githubusercontent.com/63553829/91754286-88764080-eb9f-11ea-8556-bd2292a49f8a.png){: .mx-auto.d-block :}

For each input, we browse for the files directory, and *Get Fields*. In this step, is important to *Preview* the selected fields to alter/standardize each attribute. Here the focus is in a quick view of the ETL process, so format, length, and pricision of each attribute will not be covered:

![ext2](https://user-images.githubusercontent.com/63553829/91755549-acd31c80-eba1-11ea-9dfd-b650377bff5a.png){: .mx-auto.d-block :}

Next, we must configure a new MySQL connection to *extract* data to the *staging area*. After filling the settings, one should *Test* the connection, and if it's successiful, go to the next step, which is create the tables in MySQL, which can be performed in two ways: through a query in MySQL, or directly in PDI, like we do here:

![ext3](https://user-images.githubusercontent.com/63553829/91756255-d80a3b80-eba2-11ea-8210-7bbf2ada440a.png){: .mx-auto.d-block :}
![ext4](https://user-images.githubusercontent.com/63553829/91756804-b9f10b00-eba3-11ea-953c-c9ec7368c3b9.png){: .mx-auto.d-block :}

After making all these steps for all inputs, the final extraction step would be runnin the pipeline. However, this would lead to an error:

![ext5](https://user-images.githubusercontent.com/63553829/91757255-6501c480-eba4-11ea-9dd7-d53fde833c03.png){: .mx-auto.d-block :}

If we take a closer look in *PRODUCT_DESCRIPTION*, we'd notice that *Data Type TINYTEXT* would not be able to cover all chars present in that attribute, so we must change the PDI SQL query when creating the table for the product in the *staging area* from *TINYTEXT* to *LONGTEXT*:

![ext6](https://user-images.githubusercontent.com/63553829/91757414-a4c8ac00-eba4-11ea-961d-af6ec0d5bf38.png){: .mx-auto.d-block :}

Now, if we run our pipeline, the **extraction** procedure will be successful:

![ext7](https://user-images.githubusercontent.com/63553829/91757776-46e89400-eba5-11ea-80fc-27107b88878b.png)

Done! Teh first step was taken: we took care of our **extraction!** If one wants, one can query in MySQL to check it out:

```javascript
SELECT * FROM table;
```

Check out [Part 2](https://renan2scarvalho.github.io/2020-08-31-ETL-Pentaho-Pt2/) for more **ETL!**
