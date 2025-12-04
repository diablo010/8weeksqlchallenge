# 🍊 Case Study #8 - Fresh Segments

## 🧼 Solution - A. Data Exploration and Cleansing

### 1. Update the `fresh_segments.interest_metrics` table by modifying the `month_year` column to be a date data type with the start of the month.

```sql
alter table fresh_segments.interest_metrics
alter column month_year set data type date using to_date(month_year, 'mm-yyyy');
```

**Answer:**

<img width="230" height="231" alt="Screenshot 2025-12-04 144212" src="https://github.com/user-attachments/assets/c704961c-ad84-4f02-bdcb-b8e7a7d2b47b" />

### 2. What is count of records in the `fresh_segments.interest_metrics` for each `month_year` value sorted in chronological order (earliest to latest) with the null values appearing first?

```sql
select
	month_year,
	count(*) as record_count
from fresh_segments.interest_metrics
group by month_year
order by month_year asc nulls first;
```

**Answer:**

<img width="344" height="235" alt="Screenshot 2025-12-04 144712" src="https://github.com/user-attachments/assets/c5e4078e-7389-476f-8ba2-1343bf939d05" />

### 3. What do you think we should do with these null values in the `fresh_segments.interest_metrics`?

The null values appear in some attributes. 

The corresponding values in composition, index_value, ranking, and percentile_ranking fields are not meaningful without the specific information on interest_id and dates.

Hence, we will find the percentage of null values in the table.

```sql
select 
	round(100 * sum(case when interest_id is null then 1 end)::numeric
	/ count(*), 2) as null_pct
from fresh_segments.interest_metrics;
```

<img width="163" height="88" alt="Screenshot 2025-12-04 185302" src="https://github.com/user-attachments/assets/3de6a612-8bdf-4b2b-89b2-875055c79e16" />

The percentage of null values is 8.36% which is less than 10%, hence we can drop all the null values.

```sql
delete from fresh_segments.interest_metrics
where interest_id is null;

select 
	round(100 * sum(case when interest_id is null then 1 end)::numeric
	/ count(*), 2) as null_pct
from fresh_segments.interest_metrics;
```

<img width="167" height="91" alt="Screenshot 2025-12-04 185454" src="https://github.com/user-attachments/assets/efd0bb09-3251-46df-95b5-38d2edeb46d2" />

### 4. How many `interest_id` values exist in the `fresh_segments.interest_metrics` table but not in the `fresh_segments.interest_map` table? What about the other way around?

First, we need to change the datatype of `interest_id` from varchar to integer for joining tables.

```sql
alter table fresh_segments.interest_metrics
alter column interest_id set data type integer using interest_id::integer;
```

```sql
select 
	sum(case when map.id is null then 1 end) as not_in_metric,
	sum(case when metrics.interest_id is null then 1 end) as not_in_maps	
from fresh_segments.interest_metrics metrics
full outer join fresh_segments.interest_map map
on metrics.interest_id = map.id;
```

**Answer:**

<img width="348" height="89" alt="Screenshot 2025-12-04 191359" src="https://github.com/user-attachments/assets/74448dd6-8497-4879-a9b1-a1db46055477" />

### 5. Summarise the `id` values in the `fresh_segments.interest_map` by its total record count in this table.

```sql
select
	map.id,
	map.interest_name,
	count(*) as count
from fresh_segments.interest_map map
join fresh_segments.interest_metrics metrics
on map.id = metrics.interest_id
group by map.id, map.interest_name
order by count desc, id;
```

**Answer:**

<img width="696" height="228" alt="Screenshot 2025-12-04 231944" src="https://github.com/user-attachments/assets/7c7a9612-08f3-47c4-852a-f13a5cd6201d" />

### 6. What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where `interest_id = 21246` in your joined output and include all columns from `fresh_segments.interest_metrics` and all columns from `fresh_segments.interest_map` except from the `id` column.

Using `inner join` to perform analysis.

```sql
select 
  	map.interest_name,
  	map.interest_summary,
  	map.created_at,
  	map.last_modified,
	metrics._month,
  	metrics._year,
  	metrics.month_year,
  	metrics.interest_id,
  	metrics.composition,
  	metrics.index_value,
  	metrics.ranking,
  	metrics.percentile_ranking
from fresh_segments.interest_map map
inner join fresh_segments.interest_metrics metrics
on map.id = metrics.interest_id
where metrics.interest_id = 21246 and metrics.month_year is not null;
--	columns are listed manually as no special case in pgsql
```

**Answer:**

<img width="1764" height="235" alt="Screenshot 2025-12-05 004705" src="https://github.com/user-attachments/assets/22c9eb6b-1284-4135-bc09-2c45f176d243" />

### 7. Are there any records in your joined table where the `month_year` value is before the created_at value from the `fresh_segments.interest_map` table? Do you think these values are valid and why?

```sql
select
	count(*) as count
from fresh_segments.interest_map map
inner join fresh_segments.interest_metrics metrics
on map.id = metrics.interest_id
where metrics.month_year < map.created_at::date;
```

**Answer:**

<img width="145" height="92" alt="Screenshot 2025-12-05 005201" src="https://github.com/user-attachments/assets/9e728798-5a30-4e01-b9a2-7e2a0cf91da6" />
