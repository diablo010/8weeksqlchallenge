# 🌳 Case Study #7 - Balanced Tree Clothing Co.

## 💰 Solution - A. High Level Sales Analysis

### 1. What was the total quantity sold for all products?

```sql
select 
	p.product_name,
	sum(s.qty) as total_qty_of_products
from balanced_tree.product_details p
join balanced_tree.sales s
on p.product_id = s.prod_id
group by p.product_name;
```

**Answer:**

<img width="558" height="486" alt="Screenshot 2025-11-24 210811" src="https://github.com/user-attachments/assets/54348944-b401-44af-b721-7cdf6576fd1b" />

### 2. What is the total generated revenue for all products before discounts?

```sql
select 
	p.product_name,
	sum(s.qty) * sum(s.price) as total_revenue
from balanced_tree.product_details p
join balanced_tree.sales s
on p.product_id = s.prod_id
group by p.product_name;
```

**Answer:**

<img width="496" height="486" alt="Screenshot 2025-11-24 230002" src="https://github.com/user-attachments/assets/a2d32029-e6ed-436f-8ad4-41d03c61bf56" />

### 3. What was the total discount amount for all products?

```sql
select 
	p.product_name,
	sum(s.qty * s.discount * s.price / 100) as total_discount
from balanced_tree.product_details p
join balanced_tree.sales s
on p.product_id = s.prod_id
group by p.product_name;
```

**Answer:**

<img width="502" height="484" alt="Screenshot 2025-11-24 230405" src="https://github.com/user-attachments/assets/f32716fa-58e7-4e93-9aa7-1cef4cf1f117" />
