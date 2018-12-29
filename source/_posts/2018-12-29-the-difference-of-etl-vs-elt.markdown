---
layout: post
title: "The Difference of ETL VS ELT"
date: 2018-12-29 11:04:31 +0800
comments: true
categories: ETL ELT
---
一个人走路，才是你和风景之间的单独私会。 --龙应台 《目送》
<!-- more -->
For the last couple of decades ETL (extract, transform, load) has been the traditional approach for data warehousing and analytics. The ELT (extract, load, transform) approach changes the old paradigm. But, what’s actually happening when the “T” and “L” are switched?

ETL and ELT solve the same need:
> Billions of data and events need to be collected, processed and analyzed by businesses. The data needs to be be clean, manageable and ready to analyze. It needs to be enriched, molded and transformed. To make it meaningful.

But, the **“how”** is what’s different and leads to new possibilities in many modern data projects. There are differences in how raw data is managed, when processing is done and how analysis is performed.

In this article, we’ll demonstrate the ETL and ELT technological differences showing data engineering and analysis examples of the two approaches and summarizing **10 pros and cons of ETL and ELT.**

**The Technological Differences: Lets first align on the 3 stages - E, T, L:**

- **Extraction**: Retrieving raw data from an unstructured data pool and migrating it into a temporary, staging data repository
- **Transformation**: Structuring, enriching and converting the raw data to match the target source
- **Loading**: Loading the structured data into a data warehouse to be analyzed and used by business intelligence (BI) tools

## ETL vs. ELT: What is ETL?
ETL requires management of the raw data, including the extraction of the required information and running the right transformations to ultimately serve the business needs. Each stage - extraction, transformation and loading - requires interaction by data engineers and developers, and dealing with capacity limitations of traditional data warehouses. Using ETL, analysts and other BI users have become accustomed to waiting, since simple access to the information is not available until the whole ETL process has been completed.

![ETL](/images/post/ETL.jpeg)

## What is ELT?

In the ELT approach, after you’ve extracted your data, you immediately start the loading phase - moving all the data sources into a single, centralized data repository. With today’s infrastructure technologies using the cloud, systems can now support large storage and scalable compute. Therefore, a large, expanding data pool and fast processing is virtually endless for maintaining all the extracted raw data.

![ELT](/images/post/ELT.jpeg)

In this way, the ELT approach provides a modern alternative to ETL. However, it’s still evolving. Therefore, the frameworks and tools to support the ELT process are not always fully developed to facilitate load and processing of large amount of data. The upside is very promising - enabling unlimited access to all of your data at any time and saving developers efforts and time for BI users and analysts.

## Summarizing 10 Pros & Cons of ETL and ELT
**1. Time - Load**

ETL: Uses staging area and system, extra time to load data

ELT: All in one system, load only once

**2. Time - Transformation**

ETL: Need to wait, especially for big data sizes - as data grows, transformation time increases

ELT: All in one system, speed is not dependent on data size

**3. Time - Maintenance**

ETL: High maintenance - choice of data to load and transform and must do it again if deleted or want to enhance the main data repository

ELT: Low maintenance - all data is always available

**4. Implementation complexity**

ETL: At early stage, requires less space and result is clean

ELT: Requires in-depth knowledge of tools and expert design of the main large repository

**5. Analysis & processing style**

ETL: Based on multiple scripts to create the views - deleting view means deleting data

ELT: Creating adhoc views - low cost for building and maintaining

**6. Data limitation or restriction in supply**

ETL: By presuming and choosing data a priori

ELT: By HW (none) and data retention policy

**7. Data warehouse support**

ETL: Prevalent legacy model used for on-premises and relational, structured data

ELT: Tailored to using in scalable cloud infrastructure to support structured, unstructured such big data sources

**8. Data lake support**

ETL: Not part of approach

ELT: Enables use of lake with unstructured data supported

**9. Usability**

ETL: Fixed tables, Fixed timeline, Used mainly by IT

ELT: Ad Hoc, Agility, Flexibility, Usable by everyone from developer to citizen integrator

**10. Cost-effective**

ETL: Not cost-effective for small and medium businesses

ELT: Scalable and available to all business sizes using online SaaS solutions
