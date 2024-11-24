-- SQL Retail Sales Analysis - P1
CREATE DATABASE sql_project_p2;


-- Create TABLE
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
            (
                transaction_id INT PRIMARY KEY,	
                sale_date DATE,	 
                sale_time TIME,	
                customer_id	INT,
                gender	VARCHAR(15),
                age	INT,
                category VARCHAR(15),	
                quantity	INT,
                price_per_unit FLOAT,	
                cogs	FLOAT,
                total_sale FLOAT
            );

SELECT TOP 10 *  FROM rt_sale 


    

SELECT 
    COUNT(*) 
FROM rt_sale

-- Data Cleaning
SELECT * FROM rt_sale
WHERE transactions_id IS NULL

SELECT * FROM rt_sale
WHERE sale_date IS NULL

SELECT * FROM rt_sale
WHERE sale_time IS NULL

SELECT * FROM rt_sale
WHERE 
    transactions_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantiy IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
    
-- 
DELETE FROM rt_sale
WHERE 
    transactions_id IS NULL
    OR
    sale_date IS NULL
    OR 
    sale_time IS NULL
    OR
    gender IS NULL
    OR
    category IS NULL
    OR
    quantiy IS NULL
    OR
    cogs IS NULL
    OR
    total_sale IS NULL;
    
-- Data Exploration

-- How many sales we have?
SELECT COUNT(*) as total_sale FROM rt_sale

-- How many uniuque customers we have ?

SELECT COUNT(DISTINCT customer_id) as total_sale FROM rt_sale



SELECT DISTINCT category FROM rt_sale


-- Data Analysis & Business Key Problems & Answers

-- My Analysis & Findings
-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05

select *
from  rt_sale
where sale_date = '2022-11-05'


-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022

select *
from rt_sale
where category='Clothing'
	and
	quantiy >= 4
	and
	format(sale_date, 'YYYY-MM')='2022-11'


-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.

SELECT
	category,
	sum(total_sale) as net_sale,
	count(*) as total_orders
from rt_sale
group by category



-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
select
	round(avg(age), 1) as avg_age

from rt_sale
where category = 'Beauty'


-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.

select
* 
from rt_sale
where 
	total_sale > 1000

-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.

select
	category,
	gender,
	count(*) as total_transactions
from rt_sale
group by 
	category,
	gender
order by 1



-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
select 
	year,
	month,
	avg_sale
from
(
select 
	extract(year from sale_date) as year,
	extract(month from sale_date) as month,
	avg(total_sale) as avg_sale,
	rank() over(partition by extract(year from sale_date) order by avg(total_sale) desc) as rank
from rt_sale
group by 1,2
) as t1
where rank=1;




-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
use [sql-project-p1];
select 
	top 5
	(customer_id),
	SUM(total_sale) as total_sales
from rt_sale
group by customer_id
order by 2 desc



-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.

select 
	category,
	count(distinct customer_id) as count_of_unique_cs
	from rt_sale
group by category


-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)
select * from rt_sale;

select FORMAT(hour from today())

with hourly_sales
as
(
select
	case
		when extract(hour from sale_time) <= 12 then 'Morning'
		when extract(hour from sale_time) between 12 and 17 then 'Afternoon'
		else 'Evening'
	end as shift
from rt_sale
)
select 
	shift,
	count(*) as total_orders
from hourly_sales
group by shift;
