# 🏦 Case Study #4 - Data Bank

## 💸 Solution - B. Customer Transactions


### 1. What is the unique count and total amount for each transaction type?

```sql
select 
	txn_type,
	count(customer_id) as txn_count,
	sum(txn_amount) as total_amount
from data_bank.customer_transactions
group by txn_type;
```

**Answer:**

<img width="545" height="167" alt="Screenshot 2025-10-29 210100" src="https://github.com/user-attachments/assets/2e016a0d-dd77-4cca-bde4-af81c41d7bac" />

### 2. What is the average total historical deposit counts and amounts for all customers?

```sql
with deposit as (
	select 
		customer_id,
		count(customer_id) as dep_count,
		avg(txn_amount) as dep_amount
	from data_bank.customer_transactions
	where txn_type = 'deposit'
	group by customer_id
)

select 
	round(avg(dep_count)) as avg_deposit_count,
	round(avg(dep_amount)) as avg_deposit_amt
from deposit;
```

**Answer:**

<img width="429" height="89" alt="Screenshot 2025-10-29 211921" src="https://github.com/user-attachments/assets/6cd4ae71-c1cf-4e79-9385-ccb7f90f084b" />

### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?

```sql
with monthly_transactions as (
	select
		customer_id,
		date_part('month', txn_date) as month_number,
		sum(case when txn_type = 'deposit' then 1 else 0 end) as deposit_count,
		sum(case when txn_type = 'purchase' then 1 else 0 end) as purchase_count,
		sum(case when txn_type = 'withdrawal' then 1 else 0 end) as withdrawal_count
	from data_bank.customer_transactions
	group by customer_id, month_number
)

select 	month_number,
		count(distinct customer_id) as customer_count
from monthly_transactions
where deposit_count > 1 
	and (purchase_count >= 1 or withdrawal_count >= 1)
group by month_number
order by month_number;
```

**Answer:**

<img width="404" height="197" alt="Screenshot 2025-11-01 155658" src="https://github.com/user-attachments/assets/6e862f83-5d49-4bfe-8876-b34863acafb6" />

### 4. What is the closing balance for each customer at the end of the month?

```sql
with monthly_summary as (
	select
		customer_id,
		date_part('month', txn_date) as month_number,
		sum(case when txn_type = 'deposit' then txn_amount else 0 end) as deposit_amount,
		sum(case when txn_type = 'purchase' then txn_amount else 0 end) as purchase_amount,
		sum(case when txn_type = 'withdrawal' then txn_amount else 0 end) as withdrawal_amount
	from data_bank.customer_transactions
	group by customer_id, month_number
)

select 	
	customer_id,
	month_number,
	sum(deposit_amount - (purchase_amount + withdrawal_amount))
	over (partition by customer_id order by month_number) as closing_balance
from monthly_summary
order by customer_id, month_number;
```

**Answer:**

<img width="544" height="487" alt="Screenshot 2025-11-01 161514" src="https://github.com/user-attachments/assets/ccf9ab90-4e62-4e4d-85a8-4a47af5d3a50" />

### 5. What is the percentage of customers who increase their closing balance by more than 5%?
