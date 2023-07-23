## Starting with data - Heather Lane

- provide 3-5 new questions you decided could be answered with the data
- include the answer to each questions and the accompanying queries used to obtain the answer

**Question 1:** 

Using the anaytics table (which covers May-August 2017) which days of the week saw the most visitors and the most sales? Show day name in result.

SQL Queries: 
```
SELECT	CASE 	WHEN EXTRACT(DOW FROM date) = 0 THEN 'Sunday'
		WHEN EXTRACT(DOW FROM date) = 1 THEN 'Monday'
		WHEN EXTRACT(DOW FROM date) = 2 THEN 'Tuesday'
		WHEN EXTRACT(DOW FROM date) = 3 THEN 'Wednesday'
		WHEN EXTRACT(DOW FROM date) = 4 THEN 'Thursday'
		WHEN EXTRACT(DOW FROM date) = 5 THEN 'Friday'
		ELSE 'Saturday' END AS day_of_the_week,
	COUNT(DISTINCT(visit_id)) AS number_of_visits,
	COUNT(units_sold) AS number_of_sales
FROM analytics
GROUP BY EXTRACT(DOW FROM date)
ORDER BY EXTRACT(DOW FROM date)
```

Answer: 
The highest number of visits and the highest number of sales were on Tuesdays.
```
day_of_the_week	number_of_visits	number_of_sales
Sunday		16036			8571
Monday		24086			16919
Tuesday		24386			17052
Wednesday	23807			15735
Thursday	23907			14555
Friday		21179			14457
Saturday	15469			7858
```

**Question 2:** 

What is the retail value of inventory of current "in stock" products if each product was valued at its highest possible selling price?

SQL Queries:
```
WITH cte_inventory_value AS 
	(SELECT DISTINCT(p.product_sku), 
		p.stock_level AS stock_level, 
		MAX(a.product_price) AS highest_product_price
	FROM products p
	JOIN all_sessions a
	ON p.product_sku = a.product_sku
	GROUP BY p.product_sku)

SELECT SUM(stock_level * highest_product_price)
FROM cte_inventory_value;
```

Answer:

The retail value of current in-stock inventory is $4,435,530.60 USD

NOTE:
There are multiple product prices in the table for many products probably because the price may be different in different months, or different regions so I used the highest listed price for this calculation.


**Question 3:** 

In December 2016, how many visits came from each source (channel_grouping), and what was the average time spent on the site and the average number of page views per visit for each channel grouping?


SQL Queries:
```
SELECT 	DISTINCT(channel_grouping), 
	COUNT(DISTINCT visit_id) AS number_of_visits, 
	ROUND(AVG(time_on_site), 2) AS avg_time_on_site, 
	ROUND(AVG(page_views), 2) AS avg_page_views
FROM all_sessions
WHERE time_on_site IS NOT NULL AND date BETWEEN '2016-12-01' AND '2016-12-31'
GROUP BY channel_grouping
ORDER BY number_of_visits DESC
```

Answer:
```
channel_grouping   number_of_visits	  avg_time_on_site     avg_page_views
Organic Search		444			220.51			5.43
Referral		209			180.46			5.64
Direct			183			262.07			5.37
Paid Search		52			175.96			5.94
Display			8			512.38			7.25
Affiliates		5			58.80			2.80
```

**Question 4:** 

Find the highest price listed in the table for each product, rank the products by price in each category and show the overall average price for products in that category.


SQL Queries:
```
WITH cte_highest_price_per_product AS 
		(SELECT DISTINCT(p.product_sku), 
			p.product_name, 
			a.product_category, 
			MAX(a.product_price) AS top_price
		FROM products p
		JOIN all_sessions a
		ON p.product_sku = a.product_sku
		WHERE a.product_category != '(not set)'
		GROUP BY p.product_sku, p.product_name, a.product_category)

SELECT *, RANK () OVER(PARTITION BY product_category ORDER BY top_price DESC) AS price_rank, 
	  AVG(top_price) OVER (PARTITION BY product_category) AS avg_price_by_category 
FROM cte_highest_price_per_product
ORDER BY product_category, price_rank 
```

Answer:

I did not copy the results here as the resulting report is quite large (963 rows). It contains the following columns and content:
```
product_sku		The product's SKU
product_name		The product's name
product_catagory	The category that the product is in
top_price		The highest price charged for that product
price_rank		The rank of the price compared with the price of
			other products in the same category	
avg_price_by_category	The average of the highest price per product for all
			products in that category.
```
One example: 
```
product_sku		GGOEGAAJ080616
product_name		Men's Bike Short Sleeve Tee Charcoal
product_catagory	Apparel
top_price		15.99 (highest price charged for this item)
price_rank		4 (4th highest priced product in Apparel)
avg_price_by_category	19.69125 (Average of product prices in Apparel)
```
NOTE:
There are multiple product prices in the table for each of many products, maybe because the price is different in different months, or different regions so I used the highest listed price for this calculation.


