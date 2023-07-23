## Quality Assurance - Heather Lane


### What are your risk areas? Identify and describe them.

The main risk related to all aspects of this activity and the answers developed to each question is the ambiguity of many of the items in the tables in this database. There are a lot of empty columns, columns with similar names and a lack of clarity about what the data represents.

**Missing Data**

There appeared to be a great deal of missing data particularly in the analytics and all_sessions tables.

**Primary Keys and Foreign Keys**

The analytics and all_sessions tables had no unique identifiers to use as primary keys or foreign keys. While they could probably be created I didn't feel we had the context of the data to understand how to do this in a way that made sense. I know that you can join tables without these keys by joining on columns that contain the same data elements. 

These tables had to be joined to answer the questions since all_sessions had the product, city and country data and analytics had the units_sold data.

I had to make a decision on which columns to use to join these two tables. Trying it both ways resulted in different output which I guess can be explained but making the choice with little context about what was being asked for means my results could be useless and not address the intention of the question.

**Duplicate Data**

There was a great deal of duplicate data in the all_sessions table for example. Some queries that I tried resulted in the same products turning up multiple times in the output. I discovered that this was because the same product had multiple product prices across different rows and were classified in multiple product categories across rows. Without more information it is difficult to explain whether this was a mistake or related to difference in pricing during different months or different prices and product categories in different regions or on different web pages. Getting to the bottom of this and fixing it felt way beyond the skill level of a SQL beginner but I have provided queries below that I used to find and highlight this issue.

**Different date ranges**

While responding to most of the "starting with questions" activity required the joining of all_sessions and analytics, these tables covered different date ranges so the results (assuming an inner join) only provided results for a 3 month time period. Was this the intention? There is no way to know since this was not highlighted in the questions.

**Expected rows and unexplainable results**

Related to the items described above regarding date ranges one would expect a smaller number of distinct visits and vistors from the table that covered only 3 months compared to the tables that covered a year of activity. The opposite was true which made little sense and I am not sure again whether explaining or fixing this issue is within the skills level of a SQL beginner, without more context regarding the source of the data.

**Concerns about results**

Given all the items that I have described here and the fact that fixing many of these issues felt beyond my capability I have no confidence in the results of my "starting with questions" queries even if the SQL syntax is correct. I feel slightly more comfortable with the results achieved from the "starting with data" exercise where I was able to define the question and write a query to deliver a result with a clearer understanding of the intent of the orignal question.


### QA Process:
Describe your QA process and include the SQL queries used to execute it.

In our lectures in the course the instructor talked about 6 data quality dimensions: Completeness, Validity, Accuracy, Timeliness, Consistency, and Uniqueness. So I tried to consider each of these dimensions in this exercise.

**Completeness** - refers to whether there are missing values and gaps. There most certainly were missing values and gaps in this data.

**Validity** - refers to whether the data conforms to an expected structure. There were issues with this data and some of that got fixed in the cleaning process ie. the structure of the data in the price columns and the structure of the date column. There was more that could be done here but I chose what I thought I could accomplish in the time alotted to complete this project.

**Accuracy** - is concerned with whether the data represents "true real world" values. Without more information and context I am not sure how we could determine this in this project but given the ambiguities and aspects of the data set that don't seem to make sense, the accurary is certainly suspect.

**Timeliness** - I am not sure we have enough information to comment on the Timeliness dimension in this project.

**Consistency** - While there are instances where there is consistency across tables (eg. stock level by product between the products table and the sales_report table) there are also examples of inconsistancy (eg. product_price in the all_sessions table and unit_price in the analytics tables, or number of visits recorded in a 3 month period in one table being significantly greater that number of visits recorded in another table that covered a period of 1 year).

**Uniqueness** - or the absense of duplicate records is definitely a risk with this data set and required more context and understanding of the data and much more in depth exploration than was possible in this project.

### SQL Queries used to identify and highlight some of the issues raised above:


**Missing data**

I reviewed each table to look at how many null values there were in each column using queries such as:
```
SELECT * FROM all_sessions
WHERE total_transaction_revenue IS NULL

SELECT COUNT(*) FROM analytics
WHERE unit_price IS NULL
```
some sample results from all_sessions showing the amount of missing data

--total rows in table 15,134
--total_transaction_revenue - 15,053 null
--transactions - 15,053 null
--product_refund_amount - all null 
--product_quality - 15,081 null
--product_revenue - 15,130 null 
--item_quantity - all null
--item_revenue - all null
--transaction_revenue - 15,130 null 
--search_keyword - all null
--page_path_level_one - all null
--ecommerce_action_option - 15,103 null

and missing data in the analytics table

--total rows in table 4,301,122
--userid - all null 4,301,122
--units_sold - 4,205,975 null
--bounces - 3,826,283 null
--revenue - 4,285,767 null


**Primary Keys and Foreign Keys**
```
--looking for unique identifier in analytics table

SELECT COUNT(*) AS number_rows,
		COUNT(DISTINCT(visit_id)) AS number_visits,
		COUNT(DISTINCT(full_visitor_id)) AS number_visitors,
		COUNT(DISTINCT(date)) AS number_days
FROM analytics

--result - rows 4,301,122, unique visit_id 148,642, unique visitors 120,018, unique days 93

--looking for unique identifier in all_sessions table

SELECT COUNT(*) AS number_rows,
		COUNT(DISTINCT(visit_id)) AS number_visits,
		COUNT(DISTINCT(full_visitor_id)) AS number_visitors,
		COUNT(DISTINCT(product_sku)) AS number_products,
		COUNT(DISTINCT(date)) AS number_days
FROM all_sessions

--results - rows 14,758, unique visit_id 14,192, uique visitors 13,873, unique products 411, unique days 366
```

**Duplicate Data**
```
--duplicate products due to multiple prices and product categories for one product 

SELECT DISTINCT(product_sku) FROM all_sessions

--result - 411 rows

SELECT DISTINCT(product_sku), product_category FROM all_sessions

--result - 1400 rows

SELECT DISTINCT(product_sku), product_price FROM all_sessions

--result - 717 rows

SELECT DISTINCT(product_sku), product_category, product_price FROM all_sessions
--result - 1857 rows
```

**Different date ranges**
```
--difference in date ranges between tables

SELECT MAX(date) AS end_date,
MIN(date) AS start_date
FROM analytics
--result 2017-05-01 - 2017-08-01

SELECT MAX(date) AS end_date,
MIN(date) AS start_date
FROM all_sessions
--result from 2016-08-01 - 2017-08-01
```

**Expected rows and unexplainable results**
```
--from all_sessions covering a year of activity

SELECT COUNT(*) AS number_rows,
		COUNT(DISTINCT(visit_id)) AS number_visits,
		COUNT(DISTINCT(full_visitor_id)) AS number_visitors,
		COUNT(DISTINCT(product_sku)) AS number_products,
		COUNT(DISTINCT(date)) AS number_days
FROM all_sessions

--result - rows 14,758, unique visit_id 14,192, uique visitors 13,873, unique products 411, unique days 366

--from analytics covering only 3 months of activity

SELECT COUNT(*) AS number_rows,
		COUNT(DISTINCT(visit_id)) AS number_visits,
		COUNT(DISTINCT(full_visitor_id)) AS number_visitors,
		COUNT(DISTINCT(date)) AS number_days
FROM analytics

--result - rows 4,301,122, unique visit_id 148,642, unique visitors 120,018, unique days 93
```
