**SQL retail sale analysis**
'''create database sql_project_1;'''

**select database**
use sql_project_1;

**create table**
create table retail_sales(
	transactions_id	int primary key,
    sale_date date,
    sale_time time,
    customer_id	int,
    gender varchar(10),
    age	int,
    category varchar(20),
    quantiy int,
    price_per_unit float,
    cogs float,
    total_sale float
);

**Checking for the null values**
select * from retail_sales
where transactions_id is null
	or sale_date is null
    or sale_time is null
    or customer_id is null
    or gender is null
    or age is null
    or category is null
    or quantiy is null
    or price_per_unit is null
    or cogs is null
    or total_sale is null;

**To temporarily trun off safe update mode**
SET SQL_SAFE_UPDATES = 0;

**Deleting null data**
DELETE FROM retail_sales
WHERE transactions_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantiy IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
   
**Data Exploration**

**How many sales we have?**
select count(*) from retail_sales;


**How many unique customers we have?**
select count(distinct customer_id) from retail_sales;

**How many categories we have?**
select distinct category from retail_sales;

**Data Anlysis**

**1. Write a SQL query to retrieve all columns for sales made on 2022-11-05.**
select * from retail_sales
where sale_date = '2022-11-05';

**2. Write a SQL query to retrieve all transactions where the category is clothing and the quantity sold is more than equal to 4 in the month of Nov 2022.**
select * from retail_sales
where category = 'Clothing'
	and quantiy >= 4
    and sale_date between '2022-11-01' and '2022-11-30';

**3. Write a SQL query to calculate the total sales for each category.**
select category, sum(total_sale) as total_sale from retail_sales
group by category;

**4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**
select round(avg(age),0) as age from retail_sales
where category = 'Beauty';

**5. Write a SQL query to find all transactions where the total sale is greater than 1000.**
select * from retail_sales
where total_sale > 1000;

**6. Write a SQL query to find the total number of transactions made by each gender in each category.**
select gender, category, count(transactions_id) as total_transactions from retail_sales
group by gender, category
order by gender;

**7. Wirte a SQL query to calculate the average sale for each month. Find out best selling month in each year.**
with highest_rank as(
select year(sale_date) as year, monthname(sale_date) as month, round(avg(total_sale), 0) as avg_sale,
rank() over (partition by year(sale_date) order by round(avg(total_sale), 0) desc) as rn from retail_sales
group by year, month
)
select year, month, avg_sale
from highest_rank
where rn = 1;

**8. Write a SQL query to find the top 5 customers based on the highest sales.**
select customer_id, sum(total_sale) as sales
from retail_sales
group by customer_id
order by sales desc
limit 5;

**9. Write a SQL query to find the number of unique cutomers who purchased items from each category.**
select category, count(distinct customer_id) as number_of_customers from retail_sales
group by category;

**10. Write a SQL query to create each shift and number of orders (Morning <= 12, Afternoon between 13 & 17, Evening > 17).**
with shift_transaction as(
select transactions_id, (case when sale_time <= '12:00:00' then 'Morning' 
			when sale_time between '13:00:00' and '17:00:00' then 'Afternoon' 
			Else 'Evening'
			end) as shift
from retail_sales
)
select shift, count(*) as total_orders from shift_transaction
group by shift;

**End Project**

