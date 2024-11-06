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


Answer:

"country"	"city"	"count"	"average_number_orders"
"Spain"	    "Madrid"	20	10.00
"United States"	"Other"	4214	9.85
"United States"	"Salem"	51	8.00
"United States"	"Atlanta"	66	4.00
"United States"	"Houston"	55	2.00
"United States"	"New York"	646	1.17
"United States"	"Dallas"	34	1.00
"United States"	"Chicago"	199	1.00
"Canada"	"Other"	424	1.00
"United States"	"Columbus"	2	1.00
"United States"	"Mountain View"	1175	1.00
"United States"	"San Francisco"	462	1.00
"Colombia"	"Other"	32	1.00
"France"	"Other"	154	1.00
"United States"	"Seattle"	120	1.00
"United States"	"Detroit"	4	1.00
"Argentina"	"Other"	30	1.00
"Ireland"	"Dublin"	54	1.00
"United States"	"Los Angeles"	221	1.00
"United States"	"Ann Arbor"	50	1.00
"United States"	"San Jose"	247	1.00
"Mexico"	"Other"	68	1.00
"United States"	"Palo Alto"	97	1.00
"India"	"Bengaluru"	74	1.00
"United States"	"Sunnyvale"	366	1.00
"Finland"	"Other"	14	1.00



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







