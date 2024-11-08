# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The main objective of this project is to transform and analyze web session data to gain insights into user behavior, product popularity, and transactional patterns. Key goals include identifying countries with the highest site traffic, finding the most popular products by region, and examining customer engagement patterns based on time spent on site and conversion rates.

This project aims to leverage SQL to perform data cleaning, transformation, and aggregation tasks and to generate insights for improving business strategies around site engagement and revenue growth.

## Process
### Data Cleaning (Step 1)
Standardize Missing and Null Values: Replaced values such as ‘(not set)’ in country and city with 'Other'. Set missing values in transactions, timeOnSite, and transactionRevenue to 0 to ensure consistency.
Adjust Numeric Fields for Readability: Scaled totalTransactionRevenue by dividing by 1,000,000 to make revenue numbers more interpretable.
Deduplicate Records: Identified potential duplicates based on the same fullVisitorId, productSKU, and date fields to distinguish between repeat visits and true duplicate records.

### Quality Analysis
Completeness Checks: Verified essential columns (e.g., country, city, v2ProductName, v2ProductCategory, fullVisitorId) are populated to ensure data completeness.
Range and Consistency Checks: Validated that totalTransactionRevenue and date values were within expected ranges and that transaction records had both transactionId and revenue values.
Schema Verification: Ensured data types and nullability for each column matched expectations, avoiding mismatches that could disrupt analysis.

## Results
(fill in what you discovered this data could tell you and how you used the data to answer those questions)

## Challenges 
(discuss challenges you faced in the project)

## Future Goals
(what would you do if you had more time?)
