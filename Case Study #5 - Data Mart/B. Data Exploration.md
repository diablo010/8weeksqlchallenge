# 📈 Case Study #5 - Data Mart

## 🗃️ Solution - B. Data Exploration

### 1. What day of the week is used for each week_date value?

```sql
select distinct(to_char(converted_date, 'Day')) as day_of_week	--	used to convert date, time 
														        --	into formatted string
from data_mart.clean_weekly_sales;
```

**Answer:**

<img width="207" height="93" alt="Screenshot 2025-11-02 211601" src="https://github.com/user-attachments/assets/6058f769-915d-441d-9634-a3f715df58aa" />

### 2. What range of week numbers are missing from the dataset?

```sql
with week_no_cte as (
	select generate_series(1, 52) as week_number
)

select distinct wnc.week_number
from week_no_cte as wnc
left join clean_weekly_sales as cws
on wnc.week_number = cws.week_number
where cws.week_number is null;
```

**Answer:**

<img width="212" height="411" alt="Screenshot 2025-11-02 222510" src="https://github.com/user-attachments/assets/50931522-fdec-4bf4-a27f-11292fd6b8b0" />

> Only first 10 rows are shown.

### 3. How many total transactions were there for each year in the dataset?

```sql
select calendar_year, sum(transactions) as total_transactions
from clean_weekly_sales
group by calendar_year
order by calendar_year;
```

**Answer:**

<img width="421" height="163" alt="Screenshot 2025-11-02 223048" src="https://github.com/user-attachments/assets/2bec5ef4-cfbd-462c-b584-b0d0a0730b6b" />

### 4. What is the total sales for each region for each month?

```sql
select month_number, 
	   region,
	   sum(sales) as total_monthly_sales
from clean_weekly_sales
group by month_number, region
order by month_number, region;
```

**Answer:**

<img width="648" height="308" alt="Screenshot 2025-11-02 224602" src="https://github.com/user-attachments/assets/be8b5ba5-e544-49b2-b68d-d53e2c3f52f6" />

> Only first few rows are shown.

### 5. What is the total count of transactions for each platform?

```sql
select platform,
	   sum(transactions) as total_transactions
from clean_weekly_sales
group by platform;
```

**Answer:**

<img width="453" height="126" alt="Screenshot 2025-11-02 231000" src="https://github.com/user-attachments/assets/948dd583-6fba-4fc5-964c-bc3dc913ba07" />


### 6. What is the percentage of sales for Retail vs Shopify for each month?

```sql
with monthly_sales_cte as (
	select  calendar_year, 
			month_number,
			platform,
			sum(sales) as monthly_sales
	from clean_weekly_sales
	group by calendar_year, month_number, platform
)

select
	calendar_year,
	month_number,
	round(100 * 
		sum(case
			when platform = 'Retail' then monthly_sales else 0 end)
			/ sum(monthly_sales), 2) as retail_pct,
	round(100 * 
		sum(case
			when platform = 'Shopify' then monthly_sales else 0 end)
			/ sum(monthly_sales), 2) as shopify_pct
from monthly_sales_cte
group by calendar_year, month_number
order by calendar_year, month_number;
```

**Answer:**

<img width="662" height="236" alt="Screenshot 2025-11-02 234714" src="https://github.com/user-attachments/assets/b3cf03cd-8240-428c-a8e0-9c9f42e0ef81" />

> Only first few rows are shown.

### 7. What is the percentage of sales by demographic for each year in the dataset?

```sql
with demographic_sales as (
	select
		calendar_year,
		demographic,
		sum(sales) as yearly_sales
	from data_mart.clean_weekly_sales
	group by calendar_year, demographic
)

select 
	calendar_year,
	round(100 * sum
		(case
			when demographic = 'Couples' then yearly_sales else 0 end)
			/ sum(yearly_sales), 2) as couples_pct,
	round(100 * sum
		(case
			when demographic = 'Families' then yearly_sales else 0 end)
			/ sum(yearly_sales), 2) as families_pct,
	round(100 * sum
		(case
			when demographic = 'unknown' then yearly_sales else 0 end)
			/ sum(yearly_sales), 2) as unknown_pct
from demographic_sales
group by calendar_year
order by calendar_year;
```

**Answer:**

<img width="664" height="167" alt="Screenshot 2025-11-03 123107" src="https://github.com/user-attachments/assets/5ccb2c97-c220-4ee5-a4e5-5c41af9ebdcc" />

### 8. Which age_band and demographic values contribute the most to Retail sales?

```sql
select
	age_band,
	demographic,
	sum(sales) as retail_sales,
	round(100 *
		sum(sales)
			/ sum(sum(sales)) over ()	--	nested aggregate is not allowed sum(sum(sales))
		, 1) as contribution_pct		--	outer sum acts as window
from data_mart.clean_weekly_sales
where platform = 'Retail'
group by age_band, demographic
order by retail_sales desc;
```

**Answer:**

<img width="638" height="304" alt="Screenshot 2025-11-03 124925" src="https://github.com/user-attachments/assets/61dbeb84-e825-4cf4-b8b4-e1c8b743ce27" />

### 9. Can we use the `avg_transaction` column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

```sql
select
	calendar_year,
	platform,
	round(avg(avg_transaction), 0) as avg_transaction_row,
	sum(sales) / sum(transactions) as avg_transaction_group
from data_mart.clean_weekly_sales
group by calendar_year, platform
order by calendar_year, platform;
```

**Answer:**

<img width="867" height="272" alt="Screenshot 2025-11-03 130741" src="https://github.com/user-attachments/assets/e220ccca-2b31-4fee-9ff3-5b975b4148f7" />
