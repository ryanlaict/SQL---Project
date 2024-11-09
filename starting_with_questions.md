Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**
```
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
```
```
select * --calling the created CTE above
From countries_transactionrevenue
where TRANSACTION_RANK < 3 -- Looking at all top ranked countries 
```
Answer:
The top 3 countries and their cities are United States: San Francisco, Israel: Tel Aviv and Australia:Sydney.

![image](https://github.com/user-attachments/assets/bd5de440-89e8-47f7-9ea7-0ab0b66fbdea)




**Question 2: What is the average number of products ordered from visitors in each city and country?**

```
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
```
Answer
![image](https://github.com/user-attachments/assets/50bddeec-7e67-41b3-baa6-b6504a41b36e)


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```
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

```
Answer:

![image](https://github.com/user-attachments/assets/bfb1b2f3-26a9-4ccf-ae4e-84b098d60f47)


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```
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
```
```
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
```
Answer:

![image](https://github.com/user-attachments/assets/d8a8435d-5c43-49f6-a7e8-1547a9a6ec47)



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```
--Create a CTE to calculate total transaction revenue by country and city and also overall total transaction revenue. 
With total_revenue as (
		SELECT
			COUNTRY,
			CITY,
			SUM(TOTALTRANSACTIONREVENUE) AS COUNTRY_REVENUE, --Sum totaltransaction Revenue to be aggrergated by country and city
			(
				SELECT
					SUM(TOTALTRANSACTIONREVENUE)
				FROM
					CLEANEDSESSIONS
			) AS TOTAL_TRANSACTION_REVENUE -- Subquery to pull the full totaltransaction revenue to be able to calculate percentage of revenue
		FROM
			CLEANEDSESSIONS
		Where totaltransactionrevenue > 0
		GROUP BY
			1,
			2

)
```
```
--Query to break it down by country 
SELECT
	COUNTRY,
	COUNTRY_REVENUE,
	TOTAL_TRANSACTION_REVENUE,
	(COUNTRY_REVENUE / TOTAL_TRANSACTION_REVENUE) * 100 AS PERCENTAGE_REVENUE --Caculating country'
FROM
	TOTAL_REVENUE
ORDER BY
	PERCENTAGE_REVENUE DESC
	
	--Query to break the percentage down by US cities
SELECT
	COUNTRY,
	city -- adding city to be able to determine which cities within the united states contributed the most
	COUNTRY_REVENUE,
	TOTAL_TRANSACTION_REVENUE,
	(COUNTRY_REVENUE / TOTAL_TRANSACTION_REVENUE) * 100 AS PERCENTAGE_REVENUE --Calculating country's percentage of total revenue
FROM
	TOTAL_REVENUE
WHERE
	COUNTRY = 'United States'
ORDER BY PERCENTAGE_REVENUE DESC

```
Answer:
![image](https://github.com/user-attachments/assets/bf1e8b42-9ba4-4549-bf2f-fa90a1a42b0a)

![image](https://github.com/user-attachments/assets/c76cbe59-4ed9-4f68-aa81-8a9c438b71ed)

To best summarize the impact of revenue by country and city. I made two pie charts. The first is a breakdown of revenue by countries. As the US was a signficant portion of it. I decided to create an additional query to further breakdown the United States by city to determine which cities contributed the most. 






