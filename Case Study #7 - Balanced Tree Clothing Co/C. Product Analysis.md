# 🌳 Case Study #7 - Balanced Tree Clothing Co.

## 📦 Solution - C. Product Analysis

### 1. What are the top 3 products by total revenue before discount?

```sql
select 
	pd.product_name,
	sum(s.qty * s.price) as total_revenue
from balanced_tree.sales s
join balanced_tree.product_details pd
on s.prod_id = pd.product_id
group by product_name
order by total_revenue desc
limit 3;
```

**Answer:**

<img width="472" height="162" alt="Screenshot 2025-12-02 215351" src="https://github.com/user-attachments/assets/740f287a-0a9b-436a-9e21-bed3e107f8b2" />

### 2. What is the total quantity, revenue and discount for each segment?

```sql
select 
	pd.segment_id,
	pd.segment_name,
	sum(s.qty) as total_qty,
	sum(s.qty * s.price) as total_revenue,
	sum(s.qty * s.discount * s.price / 100) as total_discount
from balanced_tree.sales s
join balanced_tree.product_details pd
on s.prod_id = pd.product_id
group by pd.segment_id, pd.segment_name;
```

**Answer:**

<img width="812" height="194" alt="Screenshot 2025-12-02 220226" src="https://github.com/user-attachments/assets/0a9f3427-1600-43f7-8ee3-7609671a1e2f" />

### 3. What is the top selling product for each segment?

```sql
with top_cte as (
	select 
		pd.segment_id,
		pd.segment_name,
		pd.product_name,
		sum(s.qty) as total_qty,
		rank() over (
			partition by segment_id
			order by sum(s.qty) desc) as rank_no
	from balanced_tree.sales s
	join balanced_tree.product_details pd
	on s.prod_id = pd.product_id
	group by pd.segment_id, pd.segment_name, pd.product_name
)

select
	segment_id,
	segment_name,
	product_name,
	total_qty
from top_cte
where rank_no=1;
```

**Answer:**

<img width="789" height="197" alt="Screenshot 2025-12-02 221635" src="https://github.com/user-attachments/assets/eeaf51a7-bad6-41dd-b824-19d79d6fafdc" />

### 4. What is the total quantity, revenue and discount for each category?

```sql
select 
	pd.category_id,
	pd.category_name,
	sum(s.qty) as total_qty,
	sum(s.qty * s.price) as total_revenue,
	sum(s.qty * s.discount * s.price / 100) as total_discount
from balanced_tree.sales s
join balanced_tree.product_details pd
on s.prod_id = pd.product_id
group by pd.category_id, pd.category_name;
```

**Answer:**

<img width="815" height="124" alt="Screenshot 2025-12-02 222047" src="https://github.com/user-attachments/assets/cea6abf1-34a7-4837-b73c-b4f7c4193caf" />

### 5. What is the top selling product for each category?

```sql
with top_cte as (
	select 
		pd.category_id,
		pd.category_name,
		pd.product_name,
		sum(s.qty) as total_qty,
		rank() over (
			partition by category_id
			order by sum(s.qty) desc) as rank_no
	from balanced_tree.sales s
	join balanced_tree.product_details pd
	on s.prod_id = pd.product_id
	group by pd.category_id, pd.category_name, pd.product_name
)

select
	category_id,
	category_name,
	product_name,
	total_qty
from top_cte
where rank_no=1;
```

**Answer:**

<img width="773" height="124" alt="Screenshot 2025-12-02 224218" src="https://github.com/user-attachments/assets/e05b8d5f-e424-4041-ab59-efec9559be60" />

### 6. What is the percentage split of revenue by product for each segment?

```sql
with revenue_cte as (
	select 
		pd.segment_id,
		pd.segment_name,
		pd.product_name,
		sum(s.qty * s.price) as total_revenue
	from balanced_tree.sales s
	join balanced_tree.product_details pd
	on s.prod_id = pd.product_id
	group by pd.segment_id, pd.segment_name, pd.product_name
),
segment_totals as (
	select
		segment_id,
		sum(total_revenue) as segment_revenue
	from revenue_cte
	group by segment_id
)

select
	r.segment_name,
	r.product_name,
	r.total_revenue,
	round(100 * r.total_revenue / st.segment_revenue) as pct_split
from revenue_cte r
join segment_totals st
on r.segment_id = st.segment_id
order by r.segment_name;
```

**Answer:**

<img width="807" height="482" alt="Screenshot 2025-12-03 091343" src="https://github.com/user-attachments/assets/82e277d1-80ca-4e50-99c9-874f4b799b25" />

### 7. What is the percentage split of revenue by segment for each category?

```sql
with revenue_cte as (
	select 
		pd.category_id,
		pd.category_name,
		pd.segment_name,
		sum(s.qty * s.price) as total_revenue
	from balanced_tree.sales s
	join balanced_tree.product_details pd
	on s.prod_id = pd.product_id
	group by pd.category_id, pd.category_name, pd.segment_name
),
category_totals as (
	select
		category_id,
		sum(total_revenue) as category_revenue
	from revenue_cte
	group by category_id
)

select
	r.category_name,
	r.total_revenue,
	r.segment_name,
	round(100 * r.total_revenue / st.category_revenue) as pct_split
from revenue_cte r
join category_totals st
on r.category_id = st.category_id
order by r.category_name;
```

**Answer:**

<img width="726" height="200" alt="Screenshot 2025-12-03 091908" src="https://github.com/user-attachments/assets/d236c6ba-78d3-445d-86a7-054879e9e300" />

### 8. What is the percentage split of total revenue by category?

```sql
with revenue_cte as (
	select 
		pd.category_id,
		pd.category_name,
		sum(s.qty * s.price) as total_revenue
	from balanced_tree.sales s
	join balanced_tree.product_details pd
	on s.prod_id = pd.product_id
	group by pd.category_id, pd.category_name
),
total_cte as (
	select 
		sum(total_revenue) as overall_revenue
	from revenue_cte
)	--	cross join is implemented as overall_revenue will be same for all

select
	r.category_name,
	r.total_revenue,
	round(100 * r.total_revenue / t.overall_revenue) as pct_split
from revenue_cte r
cross join total_cte t	--	no common key required for cross join
order by r.category_name;
```

**Answer:**

<img width="528" height="126" alt="Screenshot 2025-12-03 094033" src="https://github.com/user-attachments/assets/af1590b5-ab97-44d3-bc7b-59800477aa12" />

### 9. What is the total transaction “penetration” for each product? (hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions)

```sql
with product_cte as (
	select 
		prod_id,
	    count(distinct txn_id) as product_present
	from balanced_tree.sales
	where qty is not null
	group by prod_id
	order by prod_id
),
total_cte as (
	select
		count(distinct txn_id) as total_transactions
	from balanced_tree.sales
)

select 
	p.prod_id,
	p.product_present,
	t.total_transactions,
	round(p.product_present:: numeric / t.total_transactions::numeric, 3) as product_penetration
from product_cte p
cross join total_cte t;
--	integer/integer=integer so, numerator directly becomes 0 
--	and gives 0 as o/p hence, typecasting is used
```

**Answer:**

<img width="823" height="482" alt="Screenshot 2025-12-03 122703" src="https://github.com/user-attachments/assets/2651a08f-16be-4c6f-b0e2-1f773a66dc0e" />