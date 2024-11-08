# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The main objective of this project is to transform and analyze web session data to gain insights into user behavior, product popularity, and transactional patterns. Key goals include identifying countries with the highest site traffic, finding the most popular products by region, and examining customer engagement patterns based on time spent on site and conversion rates.

This project aims to use SQL to perform data cleaning, transformation, and aggregation tasks and to generate insights into the dataset for customer behaviours across countries and cities. 

## Process
### Data Cleaning (Step 1)
Standardize Missing and Null Values: Replaced values such as ‘(not set)’ in country and city with 'Other'. Set missing values in transactions, timeOnSite, and transactionRevenue to 0 to ensure consistency.
Adjust Numeric Fields for Readability: Scaled totalTransactionRevenue by dividing by 1,000,000 to make revenue numbers more interpretable.
Duplicate Records: Identified potential duplicates based on the same fullVisitorId, productSKU, and date fields to distinguish between repeat visits and true duplicate records.

### Quality Analysis
Completeness Checks: Verified essential columns (e.g., country, city, v2ProductName, v2ProductCategory, fullVisitorId) are populated to ensure data completeness.

Validated that totalTransactionRevenue and date values were within expected ranges and that transaction records had both transactionId and revenue values.

Ensured data types and nullability for each column matched expectations, avoiding mismatches that could skew analysis.

## Results
United States was by far and large the largest customer base with roughtly 90% of the revenue being generated by them. They also contributed to the highest level of traffic to the site. Within the United States, the highest known cities that generated the most transaction revenue were San Franciso, Sunnyvale and Atlanta. The most popular categories were NEST cameras, home products and apparel in this dataset across many countries. 

Channel Grouping also lead to interesting results with Direct and Referrals resulted in the highest transaction Revenue and also the highest average time on site. 
## Challenges 
Challenges that were face were significant missing data. Two main datapoints, Country and City had many listed as 'Other' or not set or Not Available. This limited the analysis that could have been done in terms of losing signficant portions of the dataset when performing the analysis and removing nulls. 
There was also limited data surrounding transaction revenue. There were minimal order ids with actual transactions which were an issue. The product tables were not able to be joined limiting some analysis that could be done connecting tables. 

## Future Goals
Perform more detailed analysis surround productSKUs, being able to breakdown demographic and behaviours of visitors. Being able to spend more time aggregating the data
