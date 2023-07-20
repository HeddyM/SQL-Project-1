Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:
SELECT 	country, 
	SUM(total_transaction_revenue/1000000) AS transaction_revenue 
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL
GROUP BY country
ORDER BY transaction_revenues DESC

SELECT 	city, 
	SUM(total_transaction_revenue/1000000) AS transaction_revenue 
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL AND city != 'not set'
GROUP BY city
ORDER BY transaction_revenues DESC

Answer: 
The country with the highest transaction revenue is the United States.
The city with the highest transaction reveune is San Francisco

Note: I used the total_transaction_revenue colum of the all_sessions table for 
this query even though all but 80 of the entries in this column were NULL. 
This was the most straight forward approach but with a clearer understanding of 
thedata set and perhaps more contact available I may have approached this
question differently.

**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:
--average number of different products ordered - country

WITH cte_number_products_ordered AS 
	(SELECT al.country, 
	COUNT(DISTINCT(al.product_sku)) AS number_of_diff_products_by_country,
	an.units_sold
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE country != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.country, an.units_sold)

SELECT AVG(number_of_diff_products_by_country) AS avg_number_diff_products_country
FROM cte_number_products_ordered;

--average number of different products ordered - city

WITH cte_number_products_ordered AS
	(SELECT al.city, 
	COUNT(DISTINCT(al.product_sku)) AS number_of_diff_products_by_city, 
	an.units_sold
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE city != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.city, an.units_sold)

SELECT AVG(number_of_diff_products_by_city) AS avg_number_diff_products_city
FROM cte_number_products_ordered;

--average number of individual items - country

WITH cte_number_products_ordered AS 
	(SELECT al.country, 
	SUM(an.units_sold) AS number_of_items_by_country
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE country != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.country, an.units_sold)

SELECT AVG(number_of_items_by_country) AS avg_number_items_country
FROM cte_number_products_ordered;

--average number of individual items - city

WITH cte_number_products_ordered AS 
	(SELECT al.city, 
	SUM(an.units_sold) AS number_of_items_by_city
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE city != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.city, an.units_sold)

SELECT AVG(number_of_items_by_city) AS avg_number_items_city
FROM cte_number_products_ordered;

Answer:
I could have interpreted this questions two ways to I have provided both options.

1. If the question means the average number of different products ie. dog bowls, 
knapsacks, notebooks each count as 1 regardless of volume of each item.
The average number of different roducts ordered across all countries is 3.49
The average number of different products orders across all cities is 2.26 

2. If the question means the average number of items ie. 10 dog bowls, 5
knapsacks and 2 notebooks would be 17 items.
The average number of items ordered across all countries is 21.84
The average number of items ordered across all cities is 15.20

NOTE: In both cases I used a CTE to get the count of different products or sum of 
units sold and then used the CTE result to get the average.

NOTE: In this and the next two questions it was necessary to join the all_sessions
table to the analytics table since all_sessions had country and city and analytics
had units_sold. There were two common columns (neither of which was unique on 
either table) - full_visitor_id and visit_id. Of course when you join in one
you get completely different results than when you join on the other. I tried to
explore with a mentor which was the correct one to choose. It was no clear to 
me how to make this choice so I chose one and used it consistently.

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
--product categories by country

SELECT 	al.country, 
	al.product_category, 
	SUM(an.units_sold) AS total_units_sold
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE al.country != '(not set)' AND al.product_category != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.country, al.product_category
	HAVING SUM(an.units_sold) >= 5
ORDER BY country, total_units_sold DESC

--product categories by city

SELECT 	al.city, 
	al.product_category, 
	SUM(an.units_sold) AS total_units_sold
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE al.city != '(not set)' AND al.product_category != '(not set)' AND an.units_sold IS NOT NULL
	GROUP BY al.city, al.product_category
	HAVING SUM(an.units_sold) >= 5
ORDER BY al.city, total_units_sold DESC

--to determine whethere there were sales in other cities in Canada after reviewing results

SELECT 	al.city, 
	an.units_sold 
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE country = 'Canada' AND city!= 'Toronto' 
	AND city IS NOT NULL AND an.units_sold IS NOT NULL

Answer:

Country Results
The top products categories by country (ie. most units sold from that 
category) were:

Each of these countries had only one catagories with 5 or more units sold

Canada - Home/Apparel/Headgear
Egypt - Home/Shop by Brand/Android
Israel - Home/Shop by Brand/YouTube
Japan - Home/Accessories
Switzerland - Home/Apparel/Men's/Men's-T-Shirts

The United States had 28 categories where 5 or more units were sold so
I have listed the top 5 categories here:

1. Housewares
2. Home/Bags/Backpacks
3. Home/Nest/Nest-USA
4. Home/Shop by Brand
5. Home/Apparel/Men's/Men's-Outerwear

City Results

The number of categories with 5+ units sold and the top product category by city 
(ie. most units sold from that category) were:

Chicago (4 categories) - 1. Home/Shop by Brand
Mountain View (5 categories) - 1. Home/Apparel/Men's/Men's Outerwear
New York (3 categories) - 1. Home/Bags/BackPacks
Pittsburgh (1 category) - 1. Home/Office/Notebooks & Journals
San Francisco (1 category) - 1. Home/Apparel/Women's/Women's-T-Shirts
Seattle (1 category) - 1. Home/Nest/Nest-USA
Sunnyvale (2 categories) - 1. Housewares
Tel Aviv-Yafo (1 category) - Home/Shop by Brand/YouTube
Toronto (1 category) - Home/Apparel/Headgear

Note: Toronto is the only city in Canada that had any sales recorded in 
the database and Tel Aviv_Yafo is the only city in Israel in the database 
so the city and country results are exactly the same.

Note: There were so many records for each country where just 1 items was
purchased from and category that I decided to limit the results to those 
categories where 5 or more items were ordered within each country or city.


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

SQL Queries:

--top selling product by country

SELECT 	al.country, 
	al.product_sku, 
	al.product_name,
	SUM(an.units_sold) AS total_units_sold 
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE an.units_sold IS NOT NULL 
	GROUP BY al.country, al.product_sku, al.product_name
	HAVING SUM(an.units_sold) >= 5
ORDER BY country, total_units_sold DESC;

--top selling product by city
						  
SELECT 	al.city, 
	al.product_sku, 
	al.product_name,
	SUM(an.units_sold) AS total_units_sold 
FROM all_sessions al
JOIN analytics an
ON al.visit_id = an.visit_id
	WHERE an.units_sold IS NOT NULL AND city IS NOT NULL
	GROUP BY al.city, al.product_sku, al.product_name
	HAVING SUM(an.units_sold) >= 5
ORDER BY city, total_units_sold DESC;

Answer:
The top selling product by country (where 5 or more units were ordered per 
product) are:

Canada - Android Stretch Fit Hat Black
Egypt - Android RFID Journal
Israel - YouTube Hard Cover Journal
Japan - Google Lunch Bag
United States - Google Alpine Style Backpack

The top selling product by city (where 5 or more units were ordered per 
product) are:

Chicago - Google Alpine Style Backpack
Houston - Google Sunglasses
Mountain View - Google Men's Airflow 1/4 Zip Pullover Black
New York - Google Alpine Style Backpack
Pittsburgh - You Tube Hard Cover Journal
San Francisco - Google Women's Scoop Neck Tee White
Seattle - Nest® Cam Indoor Security Camera - USA
Sunnyvale - SPF-15 Slim & Slender Lip Balm
Tel Aviv-Yafo - YouTube Hard Cover Journal
Toronto - Android Stretch Fit Hat Black

Note: Houston appeared on this list and not of the product category list 
because it was one of the items where the product category was not provided.


Note: Again to make the reults more manageable I limited the result to 
products with sales of 5 or more units (many countries had a list of 
products where only 1 unit was ordered)

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

WITH cte_transaction_revenue AS (SELECT country, SUM(total_transaction_revenue/1000000) AS transaction_revenues 
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL
GROUP BY country)
SELECT MAX(transaction_revenues),
	MIN(transaction_revenues),
	AVG(transaction_revenues)
	FROM cte_transaction_revenue;


WITH cte_transaction_revenue AS (SELECT city, SUM(total_transaction_revenue/1000000) AS transaction_revenues 
FROM all_sessions
WHERE total_transaction_revenue IS NOT NULL
GROUP BY city)
SELECT MAX(transaction_revenues),
	MIN(transaction_revenues),
	AVG(transaction_revenues)
	FROM cte_transaction_revenue;

Answer:

Some additional information on transaction revenues:

By country:
MAX - $13,154.17
MIN - $16.99
AVG - $2,856.26

By city:
MAX - $6,092.56
MIN - $16.99
AVG - $714.07


NOTE: I had no idea how to approach this question as it seemed very similar to 
question one. I chose to expand on the information in question 1 by providing
MIN, MAX, AVG information across countries and cities as a way of 
"summarizing revenue". I used the queries in question 1 as CTE's and then added
a query to get MIN, MAX and AVG.

I don't know how to summarize "impact" without additional information, other 
than to state that clearly sales and revenue are mainly based in the US. This 
may cause the company to decide to  focus on this market exclusively or to look 
at improving global reach through various forms of marketing or perhaps shipping 
discounts. 




