# 🖱️ Case Study #6 - Clique Bait

## 🖥️ Solution - B. Digital Analysis

### 1. How many users are there?

```sql
select count(distinct user_id) as total_users
from clique_bait.users;
```

**Answer:**

<img width="192" height="95" alt="Screenshot 2025-11-04 173951" src="https://github.com/user-attachments/assets/d62b0d66-ebfe-4a7d-9ffd-1f88932dee83" />

### 2. How many cookies does each user have on average?

```sql
with cookies_cte as (
    select 
		user_id, 
		count(distinct cookie_id) as cookies_per_user
	from clique_bait.users
	group by user_id
)

select
	round(avg(cookies_per_user), 2) as avg_cookies	--	aggregate functions cannot be nested
from cookies_cte;							        --	hence cte is used
```

**Answer:**

<img width="201" height="92" alt="Screenshot 2025-11-04 175245" src="https://github.com/user-attachments/assets/28964e03-1ad8-4fe3-9323-b75660153f9e" />

### 3. What is the unique number of visits by all users per month?

```sql
select 
	extract(month from event_time) as month_number,
	count(distinct visit_id)
from clique_bait.events
group by month_number;
```

**Answer:**

<img width="314" height="237" alt="Screenshot 2025-11-04 180656" src="https://github.com/user-attachments/assets/89691cf3-e309-421c-8835-1b348187767a" />

### 4. What is the number of events for each event type?

```sql
select
	event_type,
	count(event_time) as no_of_events
from clique_bait.events
group by event_type
order by event_type;
```

**Answer:**

<img width="333" height="232" alt="Screenshot 2025-11-04 180850" src="https://github.com/user-attachments/assets/a04b3aa5-0e02-429a-bcfb-c3edd5a2ddc7" />

### 5. What is the percentage of visits which have a purchase event?

```sql
select
	(100 * sum
		(case
			when ei.event_name = 'Purchase' then 1 else 0 end)
		/ count(distinct visit_id)) as puchase_visits_pct
from clique_bait.events as e
join clique_bait.event_identifier as ei
on e.event_type = ei.event_type;
```

**Answer:**

<img width="255" height="91" alt="Screenshot 2025-11-04 181704" src="https://github.com/user-attachments/assets/233d0dd6-f208-41ae-8ad2-da804b3213e0" />

### 6. What is the percentage of visits which view the checkout page but do not have a purchase event?


```sql
with checkout_but_no_purchase_cte as (
	select
		visit_id,
		sum(case when event_type = 1 and page_id = 12 then 1 else 0 end) as checkout,
		sum(case when event_type = 3 then 1 else 0 end) as purchase
	from clique_bait.events
	group by visit_id
)

select
	round(100 * (1 - (sum(purchase)::numeric
				/ sum(checkout))), 2) as checkout_but_no_purchase_cte
from checkout_but_no_purchase_cte;
```

**Answer:**

<img width="350" height="89" alt="Screenshot 2025-11-04 192541" src="https://github.com/user-attachments/assets/27dafa57-1ffd-466a-bb25-36102db462c6" />

### 7. What are the top 3 pages by number of views?

```sql
select 
	p.page_id, 
	p.page_name,
	count(e.event_type)
from clique_bait.events e
join clique_bait.page_hierarchy p
on e.page_id = p.page_id
where e.event_type = 1	--	1: Page View
group by p.page_id, p.page_name
order by count(e.event_type) desc
limit 3;
```

**Answer:**

<img width="466" height="162" alt="Screenshot 2025-11-04 193243" src="https://github.com/user-attachments/assets/a8bdd2e7-0db1-4732-825d-bc41006df29f" />

### 8. What is the number of views and cart adds for each product category?

```sql
select 
	p.product_category,
	sum(case when e.event_type = '1' then 1 else 0 end) as no_of_views,
	sum(case when e.event_type = '2' then 1 else 0 end) as no_of_cart_adds
from clique_bait.events e
join clique_bait.page_hierarchy p
on e.page_id = p.page_id
group by p.product_category;
--	when count is used instead of sum, it gives equal values for both
--	as count counts all non-null values
-- so it considers 0 as non-null
-- eg. even if o/p is 0 it will count that row
```

**Answer:**

<img width="580" height="199" alt="Screenshot 2025-11-04 194558" src="https://github.com/user-attachments/assets/bc032119-e239-4f4e-9d3a-2f71a4979924" />

### 9. What are the top 3 products by purchases?

```sql
with product_visits as (
	select
    	distinct e.visit_id,
    	p.product_category
  	from clique_bait.events e
  	join clique_bait.page_hierarchy p
    on e.page_id = p.page_id
 	where p.product_category is not null   -- only actual product pages
 ),
purchases as (
  select distinct visit_id
  from clique_bait.events
  where event_type = 3
)
select 
  pv.product_category,
  count(*) as total_purchases
from product_visits pv
join purchases p
  on pv.visit_id = p.visit_id
group by pv.product_category
order by total_purchases desc;
```

**Answer:**

<img width="435" height="164" alt="Screenshot 2025-11-04 201133" src="https://github.com/user-attachments/assets/3aba528d-bb12-4d60-8847-0c7c8ceb285b" />
