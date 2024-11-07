Question 1: Which countries have the highest level of traffic to the site, in terms of number and time spent on site

SQL Queries:

WITH COUNTRY_TRAFFIC AS (
    SELECT
        COUNTRY,
        COUNT(VISITID) AS total_visits,  -- Counts total visits per country
        ROUND(AVG(TIMEONSITE) / 60.0, 2) AS avg_time_on_site  -- Average time spent per visit in minutes
    FROM
        cleanedsessions
    WHERE
        TIMEONSITE IS NOT NULL -- Filtering for visitids with timeonsite values
    GROUP BY
        COUNTRY
    ORDER BY
        total_visits DESC
)

SELECT *
FROM COUNTRY_TRAFFIC
WHERE COUNTRY NOT IN ('(not set)')  -- Exclude records with unspecified countries
ORDER BY 
    total_visits DESC, 
    avg_time_on_site DESC;
    
Answer: 

Question 2: Which products were the most viewed by country

SQL Queries:
WITH
	PRODUCT_COUNTRY_RANKED AS (
		SELECT
			COUNTRY,--
			V2PRODUCTNAME,
			COUNT(*), --Calculating the number of times each product was viewed
			RANK() OVER (
				PARTITION BY
					COUNTRY
				ORDER BY
					COUNT(*) DESC
			) AS PRODUCT_VIEW_RANKED -- Adding a rank by views for top viewed products, partitioned by country to
		FROM
			cleanedsessions
		GROUP BY
			1,
			2 -- Group by Country and Product name 
	)

Select *
From PRODUCT_COUNTRY_RANKED
Where product_view_ranked = 1
Order by count DESC

Answer:

Question 3: How does the sentiment score affect the order quantity of a product, which country looked at the highest rated products

SQL Queries:
SELECT
    COUNTRY,
    CHANNELGROUPING,
    ROUND(AVG(TIMEONSITE / 60.0), 2) AS avg_time_onsite, -- Average time on site in minutes
    ROUND(AVG(SENTIMENTSCORE)::NUMERIC, 2) AS avg_sentiment_score, --Averaging sentiment score for products that are viewed
    ROUND((SELECT AVG(SENTIMENTSCORE) FROM SALES_REPORT)::NUMERIC, 2) AS total_average_sentiment, --bringing in the total average to compare each country against the total average 
    ROUND(AVG(SENTIMENTSCORE)::NUMERIC, 2) - ROUND(
        (SELECT AVG(SENTIMENTSCORE) FROM SALES_REPORT)::NUMERIC, 2
    ) AS difference_from_average --finding the difference between each country average vs total average 
FROM
    Cleanedsessions
JOIN 
    SALES_REPORT USING (PRODUCTSKU) --Joining the product's sentiment score to productsku
GROUP BY
    COUNTRY,
    CHANNELGROUPING
ORDER BY
    COUNTRY;

Answer:


Question 4: Which channel grouping generated the most revenue. What was the most productive productive method of referring customers and who spent the most time 

SQL Queries:

SELECT 
    COUNTRY,
    CHANNELGROUPING,
    SUM(TOTALTRANSACTIONREVENUE) AS total_revenue, --calculate the total transaction revenue 
    AVG(TIMEONSITE) AS avg_time_onsite -- calculate the average time on site
FROM 
    sessions
GROUP BY 
    COUNTRY, CHANNELGROUPING
HAVING 
    SUM(TOTALTRANSACTIONREVENUE) IS NOT NULL
ORDER BY 
    total_revenue DESC;
    
Answer:


Question 5: Looking at transactions with checkout vs timeonsite to see how long the conversate is, comparing it with customers who did not. What was the difference on site. 

SQL Queries:

WITH sessions_duration AS (
    SELECT
        TOTALTRANSACTIONREVENUE,  -- Total revenue from each session
        TIMEONSITE / 60.0 AS duration_in_mins,  -- Time spent on site in minutes
        CHANNELGROUPING,
        CASE
            WHEN TOTALTRANSACTIONREVENUE IS NOT NULL THEN 'Paying Session'
            ELSE 'Not Paying Session'
        END AS customer_type
    FROM
        sessions
)

SELECT 
    customer_type,
    CHANNELGROUPING,
    ROUND(AVG(duration_in_mins), 2) AS avg_duration_in_mins,
    SUM(TOTALTRANSACTIONREVENUE) AS total_revenue
FROM 
    sessions_duration
GROUP BY 
    customer_type, CHANNELGROUPING
ORDER BY 
    avg_duration_in_mins DESC;
Answer:
