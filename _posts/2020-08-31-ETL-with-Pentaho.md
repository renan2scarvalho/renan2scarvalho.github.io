---
layout: post
title: Extraction, Transformation, and Loading with Pentaho
subtitle: BI Fundamentals and Tools
thumbnail-img: https://pentahobrazil.files.wordpress.com/2013/12/pentaho-logo.png
#cover-img: 
#share-img: 
gh-repo: renan2scarvalho/ETL-with_Pentaho
gh-badge: [star, fork, follow]
tags: [ETL, pentaho]
comments: true
---

## Introduction

The [Gartner Group](https://www.gartner.com/en/information-technology/glossary/business-intelligence-bi) describes Business Intelligence (BI)
as the application of a group of methodologies and technologies to improve the companies operations' efficiency and 
support decision makers to reach competitive advantages. Its architecture consists basically of three main components: 

- **Extraction, Transformation, and Loading (ETL):** extract data from operational databases, perform transformations to create valid
valid information for *a posteriori* analysis (e.g. *ad hoc* reports, OLAPs) in order to support decision makers, and finally store 
this in a warehouse;
- **Data Warehouse:** data repository that apply modeling (e.g.dimensional modeling), with integrated, standardized,
detailed, and historical, to support operational, tactical, and strategic decision making;
- **Presentation:** analyze and apply front-end tools to visualize data in a friendly way, to support decision making.

(https://miro.medium.com/max/480/1*3RT78P9QznDCf1cs4-8_9Q.jpeg){: .max-auto.d-block :}

Nevertheless, ETL takes about 70% of the the total time to develop a BI application. So let's look what is the ETL process with more detail:

- **Extraction:** data is extract from Online Transaction Processing (OLTPs) with several different extentions (e.g. sql, csv, excel, json)
and is conducted to the staging area, which is basically a temporary area, where all data is standardized;
- **Transformation:** perform several adjusts, improving data quality, and consolidate data from one or more sources;
- **Load:** load the structured data into the presentation layer, following the dimensional model.

(https://www.idatamigration.com/img/etl_diagram_2.png){: .mx-auto.d-block :}

For this post, I've used Pentaho Data Integration and MySQL Workbench to realize an ETL process for a *"BI Developer"* bootcamp challenge. So let's take a quick look 
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



















