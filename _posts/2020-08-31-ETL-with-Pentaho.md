---
layout: post
title: Extraction, Transformation, and Loading with Pentaho
subtitle: BI Fundamentals and Tools
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

![ETL](https://miro.medium.com/max/480/1*3RT78P9QznDCf1cs4-8_9Q.jpeg){: .max-auto.d-block :}

For this post, I've used Pentaho Data Integration and MySQL Workbench to realize an ETL process as part of a workshop. So let's take a quick look 
over Pentaho.

## Pentaho Data Integration (PDI)

[Pentaho Data Integration](https://help.pentaho.com/Documentation/7.1/0D0/Pentaho_Data_Integration) (originally called "Kettle") is an **open source** 
software that provides the ETL capabilities
that facilitates the process of capturing, cleansing, and storing data using a uniform and consistent format that is accessible and relevant 
to end users and IoT technologies. Some common uses of Pentaho Data Integration include:

- Data migration between different databases and applications
- Loading huge data sets into databases taking full advantage of cloud, clustered and massively parallel processing environments
- Data Cleansing with steps ranging from very simple to very complex transformations
- Data Integration including the ability to leverage real-time ETL as a data source for Pentaho Reporting
- Data warehouse population with built-in support for slowly changing dimensions and surrogate key creation (as described above)

Now we now what the ETL process is about, and the tool to work with it, so let's get it done!


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

Now approaching the dimensional model, when in the DW, the PKs have another name: Surrogate Key (SK), which is basically an artificial key














