






# 📈 Case Study #5 - Data Mart

## 🔬 Solution - C. Before & After Analysis

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the `week_date` value of `2020-06-15` as the baseline week where the Data Mart sustainable packaging changes came into effect.

We would include all `week_date` values for `2020-06-15` as the start of the period after the change and the previous `week_date` values would be before.
Using this analysis approach - answer the following questions:

### 1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?

First, we find out the `week_number` associated with '2020-06-15'.

```sql
select distinct week_number
from data_mart.clean_weekly_sales
where converted_date = '2020-06-15'
	and calendar_year = '2020';
```

<img width="234" height="89" alt="Screenshot 2025-11-03 132120" src="https://github.com/user-attachments/assets/84ab32f5-f178-4c23-b970-5154aa90e6cb" />

Now, finding out the total sales for 4 weeks before and after 2020-06-15.

```sql
with packaging_sales as (
	select
		converted_date,
		week_number,
		sum(sales) as total_sales
	from data_mart.clean_weekly_sales
	where (week_number between 21 and 28)
		and (calendar_year = 2020)
	group by converted_date, week_number
)
, before_after_changes as (
	select
		sum(case
			when week_number between 21 and 24 then total_sales end) as before_packaging_sales,
		sum(case
			when week_number between 25 and 28 then total_sales end) as after_packaging_sales
	from packaging_sales
)

select
	after_packaging_sales - before_packaging_sales as sales_variance,
	round(100 *
		(after_packaging_sales - before_packaging_sales)
		/ before_packaging_sales, 2) as variance_pct
from before_after_changes;
```

**Answer:**

<img width="359" height="90" alt="Screenshot 2025-11-03 142117" src="https://github.com/user-attachments/assets/9d7dba35-f593-414f-8944-9889aab350f2" />

### 2. What about the entire 12 weeks before and after?

```sql
with packaging_sales as (
	select
		converted_date,
		week_number,
		sum(sales) as total_sales
	from data_mart.clean_weekly_sales
	where (week_number between 13 and 27)
		and (calendar_year = 2020)
	group by converted_date, week_number
)
, before_after_changes as (
	select
		sum(case
			when week_number between 13 and 24 then total_sales end) as before_packaging_sales,
		sum(case
			when week_number between 25 and 37 then total_sales end) as after_packaging_sales
	from packaging_sales
)

select
	after_packaging_sales - before_packaging_sales as sales_variance,
	round(100 *
		(after_packaging_sales - before_packaging_sales)
		/ before_packaging_sales, 2) as variance_pct
from before_after_changes;
```

**Answer:**

<img width="867" height="272" alt="Screenshot 2025-11-03 130741" src="https://github.com/user-attachments/assets/b85a49e3-ffaf-4a1b-bd75-b72594b36fe5" />

### 3. How do the sales metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

Part 1: How do the sale metrics for 4 weeks before and after compare with the previous years in 2018 and 2019?

```sql
with changes as (
	select
		calendar_year,
		week_number,
		sum(sales) as total_sales
	from data_mart.clean_weekly_sales
	where week_number between 21 and 28
	group by calendar_year, week_number
)
, before_after_changes as (
	select
		calendar_year,
		sum(case
			when week_number between 21 and 24 then total_sales end) as before_packaging_sales,
		sum(case
			when week_number between 25 and 28 then total_sales end) as after_packaging_sales
	from changes
	group by calendar_year
)

select
	calendar_year,
	after_packaging_sales - before_packaging_sales as sales_variance,
	round(100 *
		(after_packaging_sales - before_packaging_sales)
		/ before_packaging_sales, 2) as variance_pct
from before_after_changes;
```

**Answer:**

<img width="535" height="159" alt="Screenshot 2025-11-03 142313" src="https://github.com/user-attachments/assets/4d251c3e-d2ba-4f66-bb0b-3eb3a7dc051f" />

<br>

Part 2: How do the sales metrics for 12 weeks before and after compare with the previous years in 2018 and 2019?

```sql
with changes as (
	select
		calendar_year,
		week_number,
		sum(sales) as total_sales
	from data_mart.clean_weekly_sales
	where week_number between 13 and 37
	group by calendar_year, week_number
)
, before_after_changes as (
	select
		calendar_year,
		sum(case
			when week_number between 13 and 24 then total_sales end) as before_packaging_sales,
		sum(case
			when week_number between 25 and 37 then total_sales end) as after_packaging_sales
	from changes
	group by calendar_year
)

select
	calendar_year,
	after_packaging_sales - before_packaging_sales as sales_variance,
	round(100 *
		(after_packaging_sales - before_packaging_sales)
		/ before_packaging_sales, 2) as variance_pct
from before_after_changes;
```

**Answer:**

<img width="533" height="160" alt="Screenshot 2025-11-03 142354" src="https://github.com/user-attachments/assets/1eb1b09f-c676-4eb8-988d-0824875ed334" />

> Note: `week_date` attribute is `converted_date` in `clean_weekly_sales` table.