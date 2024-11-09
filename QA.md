What are your risk areas? Identify and describe them.

### In this project, there are several potential risk areas could impact data integrity, analysis accuracy, and final analysis results.

  1.) Data Quality and Completeness:
        Risk: Missing or incomplete data, especially for essential fields like country, city, timeOnSite, transactions, and transactionRevenue, could lead to inaccurate insights.
        Mitigation: We handled missing values by replacing undefined or null values with 'Other' or 0, depending on the context. Additional validation checks were performed on critical fields to ensure they 		were sufficiently populated for analysis.

   2.) Duplicate Records:
        Risk: Duplicate records, particularly repeated entries for the same fullVisitorId, productSKU, and date, could inflate metrics like total visits, views, or revenue, leading to skewed insights.
        Mitigation: We identified potential duplicates by grouping on key fields (like fullVisitorId, productSKU, and date) and investigated them to distinguish repeat visits from true duplicates. Only 		genuine duplicates were excluded from analyses.

  3.)Inconsistent or Undefined Values:
        Risk: Inconsistent values in fields like country (e.g., '(not set)') or city (e.g., 'not available in demo dataset') could disrupt analyses related to location-based insights.
        Mitigation: These values were standardized by replacing undefined entries with 'Other' to ensure consistency across analyses.

  4.)Schema and Data Type Mismatches:
        Risk: Mismatched data types or schema changes could cause errors in SQL queries or lead to inaccurate aggregations and calculations.
        Mitigation: The schema was verified to ensure data types were correctly assigned. We checked nullability constraints and verified that fields had consistent data types, reducing the risk of errors in data processing.

  5.) Outliers and Anomalies:
        Risk: Extreme values, especially in fields like totalTransactionRevenue or timeOnSite, could distort average calculations and lead to misleading insights.
        Mitigation: Basic range checks and statistical validation were performed to identify potential outliers, with further investigation into extreme values to understand if they represented genuine data 		points or errors.


### QA Process:
Describe your QA process and include the SQL queries used to execute it.
~~~
SELECT *
FROM sessions
WHERE country IS NULL -- If there are NULL countries
        OR city IS NULL -- IF there are NULL Cities
        OR v2ProductName IS NULL --If there are missing v2productnames
        OR v2ProductCategory IS NULL; --if there is missing product categories
 ~~~
~~~       
--CTE to find duplicates with VisitorID, product sku and date
WITH duplicate_ids AS (
          SELECT  fullVisitorId, 
                  productSKU,
                  date
          FROM cleanedsessions
          GROUP BY fullVisitorId, 
                    productSKU,
                    date
          HAVING COUNT(*) > 1
          )
Select *
From Duplicate_ids
 ~~~
~~~
Selecting duplicate IDS       
SELECT * 
FROM cleanedsessions
      WHERE (fullVisitorId, productSKU, date) -- che
              IN (SELECT fullVisitorId, productSKU, date 
                    FROM duplicate_ids)
ORDER BY fullVisitorId;
~~~
~~~
--Checking the max and min of transaction revenue and date
SELECT  
    MIN(totalTransactionRevenue) AS min_revenue, -- Minimum revenue recorded
    MAX(totalTransactionRevenue) AS max_revenue, -- Maximum revenue recorded
    MIN(date) AS earliest_date, -- Earliest recorded date
    MAX(date) AS latest_date    -- Latest recorded date
FROM sessions;
~~~
~~~
--Checking for null values in total transaction revenue and  transaction iD
SELECT *
    FROM sessions
    WHERE totalTransactionRevenue IS NULL 
      AND transactionId IS NOT NULL;
~~~
~~~
--Check Min/Max values for transactions & --Check First and last Dates
SELECT 
    MIN(timeOnSite) AS min_time, -- min time spent on site in seconds
    MAX(timeOnSite) AS max_time  -- max time spent on site in seconds
FROM sessions;
~~~

~~~
-- Create a view to clean and standardize data in the sessions table. 
CREATE OR REPLACE VIEW cleanedsessions AS 
    (SELECT
        fullVisitorId, -- unique visitorID
        channelGrouping,
	      Case When country = '(not set)' then 'Other'
	        Else Country
	          End as country, --replacing Not set countries with other
        Case When city in ('(not set)', 'not available in demo dataset') then 'Other' 
	        Else city
	        End as city, -- cleaning up city (replacing Not Set or not available in this dataset with Other)
        totalTransactionRevenue/1000000 as totaltransactionrevenue, 
        COALESCE(transactions, 0) AS transactions, -- replace empty transactions
	      date,
        COALESCE(timeOnSite, 0) AS timeOnSite,  -- Fill missing time on site with 0
        pageviews,
        sessionQualityDim,
        visitId, --duplicates depending on visitors
        type,
        productPrice,
        productSKU, --productID that can be used to identify specific products
        v2ProductName, --name of product
        v2ProductCategory,--not unique
        productVariant,
	      productquantity,
        currencyCode,
        COALESCE(transactionRevenue, 0) AS transactionRevenue,  -- Fill missing transaction revenue with 0
        transactionId, --ID for specific transactions - not complete
        pageTitle,
        pagePathLevel1,
        eCommerceAction_type,
        eCommerceAction_step,
        eCommerceAction_option
	FROM sessions
	)
~~~
~~~
---Call all rows from the newly created view for review
SELECT *
FROM cleanedsessions;
~~~

## CTE Summary
```
--CTE to find duplicates with VisitorID, product sku and date
WITH duplicate_ids AS (
          SELECT  fullVisitorId, 
                  productSKU,
                  date
          FROM cleanedsessions
          GROUP BY fullVisitorId, 
                    productSKU,
                    date
          HAVING COUNT(*) > 1
          )
Select *
From Duplicate_ids
```

##Temporary View
```
-- Create a view to clean and standardize data in the sessions table. 
CREATE OR REPLACE VIEW cleanedsessions AS 
    (SELECT
        fullVisitorId, -- unique visitorID
        channelGrouping,
	      Case When country = '(not set)' then 'Other'
	        Else Country
	          End as country, --replacing Not set countries with other
        Case When city in ('(not set)', 'not available in demo dataset') then 'Other' 
	        Else city
	        End as city, -- cleaning up city (replacing Not Set or not available in this dataset with Other)
        totalTransactionRevenue/1000000 as totaltransactionrevenue, 
        COALESCE(transactions, 0) AS transactions, -- replace empty transactions
	      date,
        COALESCE(timeOnSite, 0) AS timeOnSite,  -- Fill missing time on site with 0
        pageviews,
        sessionQualityDim,
        visitId, --duplicates depending on visitors
        type,
        productPrice,
        productSKU, --productID that can be used to identify specific products
        v2ProductName, --name of product
        v2ProductCategory,--not unique
        productVariant,
	      productquantity,
        currencyCode,
        COALESCE(transactionRevenue, 0) AS transactionRevenue,  -- Fill missing transaction revenue with 0
        transactionId, --ID for specific transactions - not complete
        pageTitle,
        pagePathLevel1,
        eCommerceAction_type,
        eCommerceAction_step,
        eCommerceAction_option
	FROM sessions
	)
```
