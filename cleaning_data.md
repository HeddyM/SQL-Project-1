What issues will you address by cleaning the data?

There were many possible issues to address when reviewing the tables in this
data set. I chose to do address the following 7 issues.

Issue 1: I looked for NULL fields and there were many across many columns. 
While not needed for any aspects of the project, I took the opportunity to 
practice changing the NULLs to the average value for one record (the only 
NULL) in the columns sentiment_score and sentiment_magnitude in the products 
table.

Issue 2: I removed one value in the page_tile column that was 580 characters 
long and was clearly not a page title. Rather than type out the 580 characters, 
I found the corresponding full_visitor_id for this record (and ensured it was 
unique to this record) and used it in the query to remove the inconsistent 
value. 

Issue 3: I changed the "date" field in two tables from string to date format.

Issue 4: I transformed the data in the unit_price field in the analytics table
and the product_price in the all-sessions table to the proper amount by 
dividing the amounts by 1,000,000 as suggested in the project hint.

Issue 5: I connected the products table and the sales_report table by making
product_sku a foreign key.

Issue 6: There were two different entries in the city column of the all-sessions
table to denote a record where city was not available. They were "(not set)" and 
"not available in demo dataset". I could have changed them to NULL but instead
just changed the longer string to "(not set)" for consistency.

Issue 7: When working on one of the questions I discovered from an initial result
that one record had "${eacCatTitle}" in the product_category column of the 
all-sessions table so I wrote a query to see if there were more. I found 19 records 
that had this value in that column. In a real world situation I would use the 
product_sku and/or product_name to determine the appropriate product category and 
replace these values. There was no discernable pattern (ie. there were all kinds of 
different items with this incorrect entry such as apparel, chargers, nest batteries, 
laptops, pens, etc.) so for the purpose of this project I changed them to "(not set)"
a place holder used elsewhere in this column and other columns in the table.


Queries:
Issues 1:
--checked all columns of all tables to determine number of NULL cells using 
this query...just including one example

SELECT * FROM products
	WHERE product_name IS NULL;

--found the average to replace NULL in sentiment_score and sentiment_magnitude

SELECT  AVG(sentiment_score) AS avg_ss,
        AVG(sentiment_magnitude) AS avg_sm
    FROM products;

-- queries to replace those NULLS with average score (result was avg_ss was 
0.4 and avg_sm was 0.8)

UPDATE products
    SET sentiment_score =
    COALESCE(sentiment_score, '0.4');

UPDATE products
    SET sentiment_magnitude =
    COALESCE(sentiment_magnitude, '0.8');

Issue 2:

--to find the 580 character entry in page_tile column

SELECT * FROM all_sessions
ORDER BY LENGTH(page_title) DESC;

--then after noting full_visitor_id for that record ensured it was 
unique

SELECT * FROM all_sessions
WHERE full_visitor_id = '2170054939303871324'
ORDER BY LENGTH(page_title) DESC;

--replaced it will NULL

UPDATE all_sessions 
SET page_title = NULL
WHERE full_visitor_id = '2170054939303871324';

Issue 3:
-- fixed date format in two tables

ALTER TABLE all_sessions
ALTER COLUMN date TYPE DATE USING date::date;

ALTER TABLE analytics
ALTER COLUMN date TYPE DATE USING date::date;

Issue 4:
-- fixed price columns in two tables 

UPDATE analytics
SET unit_price = (unit_price/1000000);

UPDATE all_sessions
SET product_price = (product_price/1000000);

Issue 5:
--decided to add FK to connect sales_report to products

ALTER TABLE sales_report
ADD CONSTRAINT contraint_fk
FOREIGN KEY(product_sku)
REFERENCES products(product_sku);

Issue 6:

--changed city "not available in demo dataset" to "(not set)" for
consistency in how missing values were recorded

UPDATE all_sessions
SET city = '(not set)'
WHERE city = 'not available in demo dataset'; 

Issue 7:
--to find other records with this mistake in product_category

SELECT * FROM all_sessions
WHERE product_category = '${escCatTitle}';

--to replace with (not set)
UPDATE all_sessions
SET product_category = '(not set)'
WHERE product_category = '${escCatTitle}';

