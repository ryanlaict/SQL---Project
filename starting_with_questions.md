Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

With countries_transactionrevenue as (SELECT
	COUNTRY, --selecting countries
	CITY, --selecting cities
	SUM(TOTALTRANSACTIONREVENUE) AS SUM_TOTAL_TRANSACTION, --calculating total transaction revenue
	RANK() OVER (
		PARTITION BY
			COUNTRY
		ORDER BY
			SUM(TOTALTRANSACTIONREVENUE) DESC
	) AS TRANSACTION_RANK --Ranking the total transaction partitioned by countries and 
FROM
	CLEANEDSESSIONS
WHERE
	TOTALTRANSACTIONREVENUE IS NOT NULL -- Only looking at orders that have a transaction value attached to it 
GROUP BY
	1,
	2 --group by country and city
ORDER BY
	SUM(TOTALTRANSACTIONREVENUE) DESC --order the output by largest to smallest
	)

select * --calling the created CTE above
From countries_transactionrevenue
where TRANSACTION_RANK < 3 -- Looking at all top ranked countries 

Answer:
The top 3 countries and their cities are United States: San Francisco, Israel: Tel Aviv and Australia:Sydney. The top city in the dataset for the United States was other but due to that being a catch-all for undefined cities in the US, we are unable to determine 



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT
	COUNTRY, --Selecting Country
	city, --Selecting City 
	--V2PRODUCTCATEGORY,
	COUNT(V2PRODUCTNAME), --calculating the total number of different products ordered by each city and country 
	ROUND(AVG(PRODUCTQUANTITY)::NUMERIC, 2) as average_number_orders
FROM
	cleanedsessions
GROUP BY
	1,2 -- group aggregates by Country and the nCity 
HAVING
	ROUND(AVG(PRODUCTQUANTITY)::NUMERIC, 2) IS NOT NULL --Only pulling information where product quantity is available to not skew data 
Order BY average_number_orders DESC

Answer


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
SELECT
	COUNTRY,
	CITY,
	V2PRODUCTCATEGORY, 
	COUNT(*) -- to show the most popular categories and trends 
FROM
	Cleanedsessions
WHERE
	TOTALTRANSACTIONREVENUE IS NOT NULL --ensuring that the product categories were part of orders
GROUP BY
	1,
	2,
	3 -- Aggregating the data by Country, city, and product category
ORDER BY
	COUNT(*) DESC


Answer:


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

WITH ProductSales AS (
    SELECT
        country,
        city,
        v2productname,
        SUM(productquantity) AS total_sales
    FROM
        cleanedsessions
	Where productquantity is not null
    GROUP BY
        country, city, v2productname
),
RankedSales AS (
    SELECT
        country,
        city,
        v2productname,
        total_sales,
        RANK() OVER (PARTITION BY country, city ORDER BY total_sales DESC) AS sales_rank
    FROM
        ProductSales
	Where total_sales is not null
)
SELECT
    country,
    city,
    v2productname,
    total_sales
FROM
    RankedSales
WHERE
    sales_rank = 1
ORDER BY
    country, city, total_sales

Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

WITH
	TOTAL_REVENUE AS (
		SELECT
			COUNTRY, -- Selecting country
			CITY, -- Selecting city 
			SUM(TOTALTRANSACTIONREVENUE) AS TOTAL_REVENUE_COUNTRY, -- Aggregating the total transaction Revenue
			COUNT(VISITID) AS NUMBER_OF_VISITORS,
			(
				SUM(TOTALTRANSACTIONREVENUE) / COUNT(V2PRODUCTNAME)
			) AVERAGE_PRODUCT_REVENUE,
			COUNT(V2PRODUCTCATEGORY) AS NUMBER_TYPES_PRODUCTS,
			(
				SELECT
					SUM(TOTALTRANSACTIONREVENUE) AS TOTAL_REVENUE
				FROM
					SESSIONS
			) --calculating the total
		FROM
			SESSIONS
		WHERE
			TOTALTRANSACTIONREVENUE IS NOT NULL
		GROUP BY
			1,
			2
	)
 -- Query to run the TOTAL_REVENUE CTE and utilize the calculated revenue percentage  
SELECT
	COUNTRY,
	CITY,
	NUMBER_OF_VISITORS,
	TOTAL_REVENUE_COUNTRY,
	AVERAGE_PRODUCT_REVENUE,
	((TOTAL_REVENUE_COUNTRY / TOTAL_REVENUE) * 100) AS PERCENTAGE_REVENUE,
	RANK() OVER (
		ORDER BY
			((TOTAL_REVENUE_COUNTRY / TOTAL_REVENUE) * 100) DESC
	) AS RANK_PERCENTAGE_REVENUE,
	RANK() OVER (
		ORDER BY
			NUMBER_OF_VISITORS DESC
	) AS RANK_TOTAL_VISITORS
FROM
	TOTAL_REVENUE

Answer:







