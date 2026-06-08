# Retail Sales Analysis & Exploration (PostgreSQL)
Author : Preksha Pokhrel

##  Project Overview
This project presents an end-to-end data engineering and exploratory data analysis (EDA) solution using **PostgreSQL**. The workflow encompasses initializing a data schema, performing strategic data cleaning to isolate structural anomalies, exploring foundational transactional metrics, and solving advanced analytical business problems. 

---
##  Project Objectives
1. **Database Setup**: Set up and configure a structured database environment utilizing robust typing and transactional boundaries.
2. **Data Cleaning & Quality Assurance**: Isolate and eliminate records containing critical missing values (`NULL`) to establish an audited data baseline.
3. **Exploratory Data Analysis (EDA)**: Map the dataset's scale, count customer unique variants, and categorize product spreads.
4. **Business Analytics Solutions**: Craft specific queries to solve explicit real-world business tracking questions
---
##  Section 1:Database Schema Architecture

```sql
-- Create Database
CREATE DATABASE sql_project_sq1;

-- Create Table
CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

##  Section 2: Data Cleaning & Integrity Check
```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```
##  Section 3: Foundational Data Exploration
```sql
--How many sales do we have?
	SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';

--How many unique Customers do we have?
select count ( DISTINCT customer_id) as Unique_customer 
FROM RETAIL_SALES

--How many unique gategory do we have?
select count ( DISTINCT category) as Unique_gategory 
FROM RETAIL_SALES
```
### Q1: Write a SQL query to retrieve all columns for sales made on '2023-09-05'
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2023-09-05';
;
```
### Q2. Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4;
;
```
### Q2. Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4;
;
```
### Q3. Write a SQL query to calculate the total sales (total_sale) for each category.:
```sql
SELECT 
	category,
	SUM (total_sale) AS net_sale,
	COUNT (*) AS total_orders
FROM retail_sales
GROUP BY category
;
```
### Q4.Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
;
```
### Q5. Write a SQL query to find all transactions where the total_sale is greater than 1000.:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
;
```
### Q6. Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4;
;
```
### Q7. Write a query to find the total revenue, total quantity sold, and the overall average price per unit across the entire dataset.
```sql
SELECT 
    SUM(total_sale) AS total_revenue,
    SUM(quantity) AS total_units_sold,
    ROUND(AVG(price_per_unit)::numeric, 2) AS average_price_per_unit
FROM RETAIL_SALES
;
```
### Q8. Retrieve all transactions for Female customers where the total_sale amount was greater than 1,000.
```sql
SELECT 
    transactions_id,
    sale_date,
    customer_id,
    category,
    total_sale
FROM RETAIL_SALES
WHERE gender = 'Female' AND total_sale > 1000
ORDER BY total_sale DESC;
;
```

### Q9. Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.: 
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_transaction
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
;
```
### Q10. Calculate the total net profit for each product category. Net profit is calculated as total_sale - cogs.
```sql
SELECT 
    category,
    SUM(total_sale) AS gross_revenue,
    SUM(cogs) AS total_cost_of_goods,
    SUM(total_sale) - SUM(cogs) AS net_profit
FROM RETAIL_SALES
GROUP BY category
ORDER BY net_profit DESC; 
```
### Q11. Write a SQL query to calculate the average sale for each month. Find out best selling month in each year:
```sql
SELECT
	YEAR,
	MONTH,
	AVG_SALES
FROM 
(
SELECT 
	EXTRACT(YEAR FROM sale_date) AS Year , 
	EXTRACT(MONTH FROM sale_date) AS Month,
	AVG(total_sale) AS avg_sales,
	RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
FROM retail_sales
GROUP BY 1, 2 
ORDER BY 1, 3 DESC
) AS t1
WHERE RANK = 1 
;
```
### Q12. Write a SQL query to find the top 8 customers based on the highest total sales
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 8
;
```
### Q13. Write a SQL query to find the number of unique customers who purchased items from each category.: 
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as unique_customers
FROM retail_sales
GROUP BY category
;
```
### Q14. Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17):
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
;

```
### Q15. Group customers based on their age into generations (e.g., Gen Z, Millennials, Gen X, Boomers) and calculate the total sales, average order value (AOV), and total quantities purchased for each group to find our most valuable demographic. 
```sql
SELECT 
    CASE 
        WHEN age BETWEEN 18 AND 25 THEN 'Gen Z'
        WHEN age BETWEEN 26 AND 41 THEN 'Millennials'
        WHEN age BETWEEN 42 AND 57 THEN 'Gen X'
        ELSE 'Baby Boomers'
    END AS demographic_cohort,
    COUNT(DISTINCT customer_id) AS unique_customers,
    SUM(total_sale) AS total_revenue,
    ROUND(AVG(total_sale)::numeric, 2) AS average_order_value,
    SUM(quantity) AS total_units_sold
FROM RETAIL_SALES
GROUP BY 1
ORDER BY total_revenue DESC;
```
### Q16. Calculate the month-over-month (MoM) growth rate of total sales across the entire dataset to track revenue momentum.
 ```sql
WITH MonthlySales AS (
    SELECT 
        TO_CHAR(sale_date, 'YYYY-MM') AS sales_month,
        SUM(total_sale) AS current_month_sales
    FROM RETAIL_SALES
    GROUP BY 1
),
LandedSales AS (
    SELECT 
        sales_month,
        current_month_sales,
        LAG(current_month_sales) OVER (ORDER BY sales_month) AS previous_month_sales
    FROM MonthlySales
)
SELECT 
    sales_month,
    current_month_sales,
    previous_month_sales,
    ROUND(
        ((current_month_sales - previous_month_sales) / previous_month_sales * 100)::numeric, 
        2
    ) AS mom_growth_percentage
FROM LandedSales;
```
### Q17. For each product category, determine its total revenue contribution percentage (market share) relative to the company's total revenue, and rank them. 
```sql
SELECT 
    category,
    SUM(total_sale) AS category_revenue,
    ROUND(
        (SUM(total_sale) / SUM(SUM(total_sale)) OVER () * 100)::numeric, 
        2
    ) AS market_share_percentage,
    RANK() OVER(ORDER BY SUM(total_sale) DESC) AS revenue_rank
FROM RETAIL_SALES
GROUP BY category;
```
### Q18. Perform a data validation check to find any rows where the listed total_sale does not equal quantiy * price_per_unit. 
```sql
SELECT 
    transactions_id,
    quantity,
    price_per_unit,
    total_sale,
    (quantity * price_per_unit) AS calculated_sale,
    ABS(total_sale - (quantity * price_per_unit)) AS variance
FROM RETAIL_SALES
WHERE total_sale <> (quantity * price_per_unit)
ORDER BY variance DESC;
```
---

## Project Context & Purpose

* **100% Independent Work:** This project was designed, structured, and executed **solely by me** to demonstrate my practical data analytics capabilities.
* **About the Data:** The dataset used for this analysis was sourced randomly from a free, publicly available online database. It is utilized strictly for educational and portfolio demonstration purposes.
* **Primary Objective:** The core focus of this repository is to showcase proficiency in **SQL**, specifically the ability to take raw, uncurated data and transform it into meaningful business insights.

## Use of AI Disclosure
* **Documentation Support:** Generative AI was used as a collaborative tool to assist in structuring, formatting, and refining this `README.md` file to ensure professional presentation and clarity.
* **Human-Executed Logic:** All underlying data analysis, database schema comprehension, and **100% of the SQL query logic** were conceptualized and written solely by me to demonstrate my technical skills.
---

## Author

**Preksha Pokhrel**
* **GitHub:** (https://github.com/prekshapokhrel) 
* **LinkedIn:** (https://www.linkedin.com/in/preksha-pokhrel/)
Thank you for taking the time to review my work! If you have any questions about the logic or methodology used in these queries, please feel free to connect.
