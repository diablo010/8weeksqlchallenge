# 🏦 Case Study #4 - Data Bank

## 💰 Solution - A. Customer Nodes Exploration


### 1. How many unique nodes are there on the Data Bank system?

```sql
select count(distinct customer_id)
from data_bank.customer_nodes;
```

**Answer:**

<img width="154" height="100" alt="Screenshot 2025-08-23 162013" src="https://github.com/user-attachments/assets/050e66ab-8f32-433a-bb75-736e55bb7770" />

### 2. What is the number of nodes per region?

```sql
select region_id, count(distinct node_id) as num_of_nodes
from data_bank.customer_nodes
group by region_id;
```

**Answer:**

<img width="272" height="238" alt="Screenshot 2025-08-23 162409" src="https://github.com/user-attachments/assets/4c65e9db-e9de-4610-a1c1-b4877019e760" />

### 3. How many customers are allocated to each region?

```sql
select region_id, count(distinct customer_id) as num_of_customers
from data_bank.customer_nodes
group by region_id;
```

**Answer:**

<img width="382" height="242" alt="Screenshot 2025-08-23 162508" src="https://github.com/user-attachments/assets/2cdc1d84-7c53-454b-8013-3a8d8b2d8c1f" />

### 4. How many days on average are customers reallocated to a different node?

```sql
select avg(end_date - start_date) as avg_days_on_node
from data_bank.customer_nodes
where  end_date != '9999-12-31';	--	this date is used to denote still active in dataset

```

**Answer:**

<img width="270" height="92" alt="Screenshot 2025-10-29 203640" src="https://github.com/user-attachments/assets/9c7900a5-2c41-49b3-a477-ce7cdaa5c867" />

### 5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?

```sql
with day_metrics as (
	select region_id, (end_date - start_date) as days_on_node
	from data_bank.customer_nodes
	where  end_date != '9999-12-31'
)

select 
	region_id,
	percentile_cont(0.5) within group (order by days_on_node) as median,
	percentile_cont(0.8) within group (order by days_on_node) as eighty_percentile,
	percentile_cont(0.95) within group (order by days_on_node) as ninetyfive_percentile
from day_metrics
group by region_id
order by region_id;

```

**Answer:**

<img width="731" height="236" alt="Screenshot 2025-10-29 205445" src="https://github.com/user-attachments/assets/bd75c4c8-278c-4d00-95cb-8b95c1fef54f" />
