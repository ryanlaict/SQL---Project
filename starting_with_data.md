**Question 1: What were the top 10 countries by visitors and Time spent on site**

**SQL Queries:**

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
        total_visits DESC --order by total visits descending
)

SELECT *
FROM COUNTRY_TRAFFIC --calling from previously created CTE 
WHERE COUNTRY NOT IN ('(not set)')  -- Exclude records with unspecified countries
ORDER BY 
    total_visits DESC, 
    avg_time_on_site DESC;
    
#Answer: 

![image](https://github.com/user-attachments/assets/4b924916-3d7b-40ac-a757-de3ce54c2eae)
Top 10 countries by Visitors

![image](https://github.com/user-attachments/assets/43f3e286-2776-422c-80dd-ca57713ed09e)
Top 10 countries by Time spent on site

**Question 2: Which products were the most viewed by country**

**SQL Queries:**
--Create CTE to rank all products by views partitioned by Country
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
From PRODUCT_COUNTRY_RANKED --Calling created above CTE: PRODUCT_COUNTRY_RANKED  
Where product_view_ranked = 1 -- Filtereing for top ranked products 
Order by count DESC

**Answer:**

![image](https://github.com/user-attachments/assets/48c9cd97-8835-4d41-9d3b-a5d7b819c3bf)

**Question 3: Top 15 countries with largest differential from Sentiment**

**SQL Queries:**

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
![image](https://github.com/user-attachments/assets/75b85575-a1c4-4993-8936-ab02fb9a0b48) -- Top 15 countries with negative difference from the overall average sentiment score
![image](https://github.com/user-attachments/assets/57fecbb6-6ee8-4ac2-ac46-327ea785c140) --top 15 countries with positive difference from the overall average sentiment score


**Question 4: Which channel grouping generated the most revenue. What was the most productive productive method of referring customers and who spent the most time **

**SQL Queries:**

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
    
**Answer:**
![image](https://github.com/user-attachments/assets/d13b94a1-7970-4958-88ab-4c17fcdc8b43)


**Question 5: Looking at transactions with checkout vs timeonsite to see how long the conversate is, comparing it with customers who did not. What was the difference on site. **

**SQL Queries:**

WITH sessions_duration AS (
    SELECT
        TOTALTRANSACTIONREVENUE,  -- Total revenue from each session
        TIMEONSITE / 60.0 AS duration_in_mins,  -- Time spent on site in minutes
        CHANNELGROUPING, --selecting the channeling grouping column
        CASE
            WHEN TOTALTRANSACTIONREVENUE IS NOT NULL THEN 'Paying Session' --Classify the visit as a paying session when the totaltransactionrevenue column is not null
            ELSE 'Not Paying Session' --if it is NULL, classify it as a non paying sessions
        END AS customer_type --ending it as a customer_type to group it by later. 
    FROM
        sessions
)

SELECT 
    customer_type,
    CHANNELGROUPING,
    ROUND(AVG(duration_in_mins), 2) AS avg_duration_in_mins, --round the average duration to a two decimal point
    SUM(TOTALTRANSACTIONREVENUE) AS total_revenue --sum the total transaction revenue 
FROM 
    sessions_duration
GROUP BY 
    customer_type, CHANNELGROUPING
ORDER BY 
    avg_duration_in_mins DESC;
    
**Answer:**
![image](https://github.com/user-attachments/assets/cdb4886c-b452-4427-b167-cdaf06216203)
