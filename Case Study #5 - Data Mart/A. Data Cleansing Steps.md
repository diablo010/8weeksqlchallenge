# 📈 Case Study #5 - Data Mart

## 🧼 Solution - A. Data Cleansing Steps

### In a single query, perform the following operations and generate a new table in the data_mart schema named `clean_weekly_sales`:
- Convert the `week_date` to a DATE format
- Add a `week_number` as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc
- Add a `month_number` with the calendar month for each week_date value as the 3rd column
- Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called `age_band` after the original `segment` column using the following mapping on the number inside the segment value:

<img width="250" height="130" alt="image" src="https://github.com/user-attachments/assets/8ae301ea-d792-4d53-9868-55867e22bc62" />

- Add a new `demographic` column using the following mapping for the first letter in the `segment` values:

<img width="250" height="110" alt="image" src="https://github.com/user-attachments/assets/16bd89cf-a11d-44be-9c1d-db5e5b0f80c8" />

- Ensure all `null` string values with an `"unknown"` string value in the original segment column as well as the new age_band and demographic columns
- Generate a new `avg_transaction` column as the sales value divided by transactions rounded to 2 decimal places for each record


```sql
create table clean_weekly_sales as		--	table created from existing table
select 
	to_date(week_date, 'dd/mm/yy') as converted_date,	--	date format for pgsql is different
	date_part('week', to_date(week_date, 'dd/mm/yy')) as week_number,
	date_part('month', to_date(week_date, 'dd/mm/yy')) as month_number,
	date_part('year', to_date(week_date, 'dd/mm/yy')) as calendar_year, 
	region,
	platform,
	segment,
	case 
		when substring(segment from 2) = '1' then 'Young Adults'	--	1-based starting position
		when substring(segment from 2) = '2' then 'Middle Aged'
		when substring(segment from 2) = '3' then 'Retirees'
		when substring(segment from 2) = '4' then 'Retirees'
		else 'unknown'
	end as age_band,
	case 
		when left(segment, 1) = 'C' then 'Couples'
		when left(segment, 1) = 'F' then 'Families'
	else 'unknown'
	end as demographic,
	customer_type,
	round((sales / transactions), 2) as avg_transaction
from data_mart.weekly_sales;
```

**Answer:**
<img width="1612" height="181" alt="data_mart1" src="https://github.com/user-attachments/assets/df9d1f7d-9efb-4569-86b9-bfb1c5e86830" />

