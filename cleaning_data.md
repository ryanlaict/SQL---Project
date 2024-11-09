What issues will you address by cleaning the data?

Through this process, I will attempted to address the following issues through cleaning the data. The cleaning process addresses missing values, standardizes entries, handles duplicates, and ensures data quality for aggregation, making the dataset more reliable and consistent for analysis.

- Handling and cleaning missing data or incomplete data
    - Deadling with 'Not set' or 'Not available'  data
- Standardization
    - Standardizing countries    
- Preparing data for furthur analysis
- 
Queries:
Below, provide the SQL queries you used to clean your data.

```
--Creation of a Temporary View and 
Create or replace view cleanedsessions as 
    (SELECT
        fullVisitorId, -- unique visitorID
        channelGrouping,
	  Case When country = '(not set)' then 'Other'
	  Else Country
	  End as country, --replacing Not set countries with other
        case When city in ('(not set)', 'not available in demo dataset') then 'Other' 
	  Else city
	  End as city, -- cleaning up city (replacing Not Set or not available in this dataset with Other)
        totalTransactionRevenue,  -- Only include rows where this column is not NULL
        COALESCE(transactions, 0) AS transactions, -- replace empty transactions
	  date,
        COALESCE(timeOnSite, 0) AS timeOnSite,  -- Fill missing time on site with 0
        pageviews,
        sessionQualityDim,
        visitId, --duplicates depending on visitors
        type,
        productPrice,
        productSKU, --productID that can be used to identify specific products
        v2ProductName,
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
        eCommerceAction_option,
	  Count(*) -- remove
    FROM sessions
    Group by 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26)
```
```
Select *
From Cleanedsessions
```
