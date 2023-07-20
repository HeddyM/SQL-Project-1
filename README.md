# Final-Project-Transforming-and-Analyzing-Data-with-SQL - Heather Lane

## Project/Goals
This project provides students with the opportunity to practice skills learned to date on a database called "ecommerce". We had the opportunity to set up tables, import data, review the data, clean, transform and analyze the data, write SQL queries to extract data in response to questions, and perform some quality assurance on the data.

My goals were simply to try and understand the data and to work through the steps of the project to the best of my ability. For most of the activity I was not confident that I was able to achieve these goals but I submit my project as my best effort.

## Process
### 1. Reveiwing Data Files

I spent time reviewing the tables and columns in the .csv files provided to determine the types of data for each column in each table. I used VARCHAR as the data types for many, INTEGER for those columns that were clearly numeric but had no decimal place and FLOAT for items that had decimal places like currency (price or revenue) and ratings like sentiment_score.

### 2. Created Tables

I set up the tables by writing CREATE TABLE queries in pgAdmin listing all the columns names and the data type for each column.

### 3. Imported Data

Once the tables were created I imported the .csv files. I had some challenges but in the end was able to figure things out.

### 4. More Data Review

I did a number of SELECT * queries on each table to look at the data in the tables and try to understand the various columns and how tables were or could be connected to each other.

### 5. General Cleaning

I started with some general cleaning before really knowing what columns of data would be needed for specific questions in the project. This included taking note of where there were NULLS, finding blank columns, and converting dates and currency into proper formats.

### 6. Writing Queries

I started to experiment with some query construction to address the "starting with questions".

### 7. More Cleaning

I cleaned up some more items in the data as needed while I worked through the questions.

### 8. Finalized Queries

Once I decided how I was going to interpret the questions and which fields I was going to use to answer them (there were many options that provide different results), I finalized the queries and recorded the answers.

### 9. Additional Queries

I created the additional questions and wrote queries to answer these.

### 10. QA

I attempted to document my "QA" activities.

### 11. Completed Documentation

I completed all of the documentation required for the project and submitted it.

## Results
This data has a lot of information about the activity of customers when they visit the company's website, how they got there, how long they stayed and how many pages they looked at. There is information on sales by city and country and about variable product pricing. It is clear by all measures that the compnay does the majority of it's business in the US.

In the "starting with data" exercise I was able to learn a lot of different things about the company's assets and activities and the behaviour of its customers. I looked at whether some days of the week were more popular ie. more visits and sales. I determined the retail value of their current in stock inventory of products. I looked at number of visits, time_on_site and page views for one specific month. Finally I determined the rank by product price of products in each product category and the average product price by category.


## Challenges 
I found this project to be very frustrating as there was no clear information about how the tables of data were connected. There was inconsistant data between tables, many columns with no data, and no where to go to learn more about the data set, how the data was collected and when, units of measure used, etc. In readings and lectures the point was made repeatedly that having intimate knowledge of, and understanding, the data set is essential in order to clean it properly, use it to answer questions and to trust the output. I did not find it possible to acieve the appropriate level of understanding in this project. 

Far too much time was required just to try and understand the context of this data set, something that I don't think I could fully achieve. I eventually had to give up trying as there was no one to consult who knew this data or the way in which the company activity it represents operates. Not being able to learn what each of the columns represented, how, where and when it was collected, etc. made understanding the context very challenging. I was left with resorting to guesses.

Importing the data was challenging as my attempts to import the data into the "all sessions.csv" file kept failing. The error messages referred to data that was larger than the character limits that I had put on my VARCHAR columns. I didn't know which column was causing it to fail so I just kept increasing the character limits but kept getting errors. I went back to the original .csv file and found that one entry in the page_title columns was 580 characters long and was not a page title. I fixed the character limit and was able to import the file. In retrospect maybe I should not have tried to put size limits on the columns until I better understood the data, but it helped me find the one weird entry in the 10,171st row. I replaced it with a NULL after the data was imported.

It was difficult to determine which data to use to answer the questions about revenue, products ordered and top selling products. The "products" table has an "order_quantity" column, the "sales_report" and "sales_by_sku" tables each have a "total_ordered" column, the "all_sessions" table has "total_transaction_revenue" (where all but 80 of over 15,000 records are NULL) and "product_revenue", "item_quantity" and "transaction_revenue"columns which were all completely blank. The "analytics" table has a "units_sold" column but it contains higher total numbers by product than the sales tables. In the end I just had to pick which one I was going to use and go with it.

I was able to create a FK between the "sales_report" and "products" tables but while I was able to join various tables on other columns, I had no confidence that the reults were clean and reliable, or that I had joined them in a way that was going to give accurate results. There did not appear to be a unique identifier in the "all_sessions" or "analytics" tables and I didn't feel that I had the skills to create one that could be used to join them to each other or to other tables. When joining these two tables there appeared to be two options ie. "visit_id" or "full_visitor_id" but each gave different results to my queries. All of this left me uneasy with the results of my queries.

Using the "all_sessions" table was necessary for all of the "starting with questions" since it was the only table with country and city data. The table has data for 1 year (the range in the date represented August 2016 - July 2017). Any orders or sales data has to come from other tables. The analytics table only contains data from May 2017 to July 2017 and the sales tables have no references to date or timeframe. 

## Future Goals

I would try to find someone who understands the data ie. what each field represents, when and how it was collected etc. This would help me to be clearer on what needed cleaning. I would spend more time trying to figure out where there were duplicates that needed attention and I might have considered removing all columns that contained no data. There appears to be many duplicates in the larger tables but while some of the product_skus and product_names are duplicates the rows with the same products have different categories or different prices. There are duplicate visit_ids but this makes sense if more than 1 product was purchased in the same visit. There is much ambiguity in these tables. I would like more context and clarity so that I could better determine which columns to use to join tables and to answer the questions.

It would be helpful to have access to other possible data sets from this same operation that could fill in the holes in this data if such data sets existed.

Finally some clarity on the intention of some of the questions and what they are asking for specifically would be helpful. Even mentors that we spoke with didn't know what was being asked for in questions 5, for example.
