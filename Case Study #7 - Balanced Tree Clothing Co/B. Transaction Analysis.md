# 🌳 Case Study #7 - Balanced Tree Clothing Co.

## 💳 Solution - B. Transaction Analysis

### 1. How many unique transactions were there?

```sql
select 
	count(distinct txn_id) as no_of_unique_txn
from balanced_tree.sales;
```

**Answer:**

<img width="239" height="89" alt="Screenshot 2025-12-01 120941" src="https://github.com/user-attachments/assets/cfd30ec0-ee82-432e-a569-e72eaa09e4a1" />

### 2. What is the average unique products purchased in each transaction?

```sql
with unique_products_cte as (
	select 
		txn_id,
		count(distinct prod_id) as unique_products
	from balanced_tree.sales
	group by txn_id
)

select 
	round(avg(unique_products)) as avg_unique_products
from unique_products_cte;	--	cte used as aggregates cannot be nested
```

**Answer:**

<img width="269" height="93" alt="Screenshot 2025-12-01 135753" src="https://github.com/user-attachments/assets/00c39e02-0948-429f-b01b-c9fc34c21c34" />

### 3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?

```sql
with revenue_cte as (
	select
		txn_id,
		sum(qty * price) as total_revenue
	from balanced_tree.sales
	group by txn_id
)

select
	percentile_cont(0.25) within group (order by total_revenue) as twentyfifth_percentile,
	percentile_cont(0.5) within group (order by total_revenue) as fiftieth_percentile,
	percentile_cont(0.75) within group (order by total_revenue) as seventyfifth_percentile
from revenue_cte;
-- group by txn_id;	--	group by runs on that single-row partition each time
```

**Answer:**

<img width="677" height="95" alt="Screenshot 2025-12-01 154049" src="https://github.com/user-attachments/assets/952c5789-1356-476c-b220-0ea453fdaf25" />

### 4. What is the average discount value per transaction?

```sql
with discount_cte as (
	select
		txn_id,
		sum(qty * price * discount / 100) as discount_value
	from balanced_tree.sales
	group by txn_id
)

select 
	round(avg(discount_value)) as avg_discount
from discount_cte;
```

**Answer:**

<img width="206" height="88" alt="Screenshot 2025-12-01 154842" src="https://github.com/user-attachments/assets/c4317ae4-8836-420a-879b-f441a9b20872" />

### 5. What is the percentage split of all transactions for members vs non-members?

```sql
with member_cte as (
	select
		sum(case when member is not null then 1 else 0 end) as total_transactions,
		sum(case 
			when member = 't' then 1 else 0 
			end) as no_of_members,
		sum(case 
			when member = 'f' then 1 else 0
			end) as no_of_non_members
	from balanced_tree.sales
)

select
	(no_of_members  * 100 / total_transactions) as pct_for_members,
	(no_of_non_members  * 100 / total_transactions) as pct_for_non_members
from member_cte;
--	easier approach: using group by instead of case when
```

**Answer:**

<img width="460" height="92" alt="Screenshot 2025-12-01 163909" src="https://github.com/user-attachments/assets/d06d727b-ef32-48d5-95c0-feb17a58a34b" />

### 6. What is the average revenue for member transactions and non-member transactions?

```sql
with member_cte as (
	select 
		member,
		txn_id,
		sum(qty * price) as total_revenue
	from balanced_tree.sales
	group by member, txn_id
)

select 
	member,
	round(avg(total_revenue), 2) as avg_revenue
from member_cte
group by member;
```

**Answer:**

<img width="313" height="130" alt="Screenshot 2025-12-01 165426" src="https://github.com/user-attachments/assets/e6f51153-9c25-43d1-9c05-7d005cabe6c5" />
