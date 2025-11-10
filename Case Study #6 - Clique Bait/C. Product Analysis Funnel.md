# 🖱️ Case Study #6 - Clique Bait

## 🔬 Solution - C. Product Analysis Funnel

**Using a single SQL query - create a new output table which has the following details:**

- How many times was each product viewed?
- How many times was each product added to cart?
- How many times was each product added to a cart but not purchased (abandoned)?
- How many times was each product purchased?

```sql
with view_cart_cte as (
	select 	
		e.visit_id,
		ph.page_name as product_name,
		ph.product_category,
		sum(case when e.event_type = 1 then 1 else 0 end) as page_views,
		sum(case when e.event_type = 2 then 1 else 0 end) as cart_adds,
		-- sum(case when e.event_type = 3 then 1 else 0 end) as purchases,
		-- case when is not applicable for 2 conditions of same row at once hence can be solved as
		-- sum(case when e.event_type = 2
		-- 	and e.visit_id not in (
		-- 		select visit_id
		-- 		from clique_bait.events
		-- 		where event_type = 3
		-- 	) then 1 else 0
		-- 	end) as cart_but_no_purchase	
	from clique_bait.events e
	join clique_bait.page_hierarchy ph
	on e.page_id = ph.page_id
	where ph.product_id is not null		-- excludes pages like checkout as they have no page_id
										-- and purchases take place at checkout as purchase 
										-- so separate cte
	group by e.visit_id, ph.page_name, ph.product_category
),
purchases as (
	select distinct visit_id
	from clique_bait.events
	where event_type = 3
)

select
	vcc.product_name,
	vcc.product_category,
	sum(vcc.page_views) as page_views,
	sum(vcc.cart_adds) as cart_adds,
	sum(case when vcc.cart_adds > 0 and p.visit_id is null then 1 else 0 end) as cart_but_no_purchase,
	sum(case when vcc.cart_adds > 0 and p.visit_id is not null then 1 else 0 end) as purchases
from view_cart_cte vcc
left join purchases p
on vcc.visit_id = p.visit_id
group by vcc.product_name, vcc.product_category;
```

**Answer:**

<img width="1074" height="375" alt="Screenshot 2025-11-10 193402" src="https://github.com/user-attachments/assets/59639a67-a2e4-41ad-af4e-c44bc6ffc42d" />

**Solution:**

```sql
WITH product_page_events AS ( -- Note 1
  SELECT 
    e.visit_id,
    ph.product_id,
    ph.page_name AS product_name,
    ph.product_category,
    SUM(CASE WHEN e.event_type = 1 THEN 1 ELSE 0 END) AS page_view, -- 1 for Page View
    SUM(CASE WHEN e.event_type = 2 THEN 1 ELSE 0 END) AS cart_add -- 2 for Add Cart
  FROM clique_bait.events AS e
  JOIN clique_bait.page_hierarchy AS ph
    ON e.page_id = ph.page_id
  WHERE product_id IS NOT NULL
  GROUP BY e.visit_id, ph.product_id, ph.page_name, ph.product_category
),
purchase_events AS ( -- Note 2
  SELECT 
    DISTINCT visit_id
  FROM clique_bait.events
  WHERE event_type = 3 -- 3 for Purchase
),
combined_table AS ( -- Note 3
  SELECT 
    ppe.visit_id, 
    ppe.product_id, 
    ppe.product_name, 
    ppe.product_category, 
    ppe.page_view, 
    ppe.cart_add,
    CASE WHEN pe.visit_id IS NOT NULL THEN 1 ELSE 0 END AS purchase
  FROM product_page_events AS ppe
  LEFT JOIN purchase_events AS pe
    ON ppe.visit_id = pe.visit_id
),
product_info AS (
  SELECT 
  	product_id,
    product_name, 
    product_category, 
    SUM(page_view) AS views,
    SUM(cart_add) AS cart_adds, 
    SUM(CASE WHEN cart_add = 1 AND purchase = 0 THEN 1 ELSE 0 END) AS abandoned,
    SUM(CASE WHEN cart_add = 1 AND purchase = 1 THEN 1 ELSE 0 END) AS purchases
  FROM combined_table
  GROUP BY product_id, product_name, product_category)

SELECT *
FROM product_info
ORDER BY product_id;
```

**Answer:**

<img width="1091" height="378" alt="Screenshot 2025-11-10 195503" src="https://github.com/user-attachments/assets/c9fa11fe-d46c-4618-9420-3be8d355d104" />

**Additionally, create another table which further aggregates the data for the above points but this time for each product category instead of individual products.**

```sql
with view_cart_cte as (
	select 	
		e.visit_id,
		ph.product_category,
		sum(case when e.event_type = 1 then 1 else 0 end) as page_views,
		sum(case when e.event_type = 2 then 1 else 0 end) as cart_adds	
	from clique_bait.events e
	join clique_bait.page_hierarchy ph
	on e.page_id = ph.page_id
	where ph.product_id is not null		-- excludes pages like checkout as they have no page_id
										-- and purchases take place at checkout as purchase 
										-- so separate cte
	group by e.visit_id, ph.product_category
),
purchases as (
	select distinct visit_id
	from clique_bait.events
	where event_type = 3
)

select
	vcc.product_category,
	sum(vcc.page_views) as page_views,
	sum(vcc.cart_adds) as cart_adds,
	sum(case when vcc.cart_adds > 0 and p.visit_id is null then 1 else 0 end) as cart_but_no_purchase,
	sum(case when vcc.cart_adds > 0 and p.visit_id is not null then 1 else 0 end) as purchases
from view_cart_cte vcc
left join purchases p
on vcc.visit_id = p.visit_id
group by vcc.product_category;
```

**Answer:**

<img width="863" height="160" alt="Screenshot 2025-11-10 193531" src="https://github.com/user-attachments/assets/b73a1c03-a851-45e5-abfb-dfc813a0d0ed" />

**Use your 2 new output tables - answer the following questions:**
**1. Which product had the most views, cart adds and purchases?**

**Answer:**
- Oyster has the most views.
- Lobster has the most cart_adds and the most purchases.

**2. Which product was most likely to be abandoned?**

**Answer:**
- Russian Caviar is most likely to be abandoned.

**3. Which product had the highest view to purchase percentage?**

```sql
select
  	product_category, 
  	round(100 * purchases/views,2) as purchase_per_view_percentage
from product_info
order by purchase_per_view_percentage desc
limit 1;
```

**Answer:**

<img width="768" height="94" alt="Screenshot 2025-11-10 195853" src="https://github.com/user-attachments/assets/3dc99cd8-887d-495c-9027-9f1f07131eed" />

**4. What is the average conversion rate from view to cart add?**

```sql
select  
  round(100 * avg(cart_adds/views),2) as avg_view_to_cart_add_rate
from product_info;
```

**Answer:**

<img width="308" height="91" alt="Screenshot 2025-11-10 200220" src="https://github.com/user-attachments/assets/8a219284-64c8-4115-9eae-3d3510ded681" />

**5. What is the average conversion rate from cart add to purchase?**

```sql
select  
  round(100 * avg(purchases/cart_adds),2) as avg_cart_add_to_purchases_rate
from product_info;
```

**Answer:**

<img width="355" height="89" alt="Screenshot 2025-11-10 200413" src="https://github.com/user-attachments/assets/4a1cbc7e-bc03-4067-99f3-d483d5faa5a0" />
