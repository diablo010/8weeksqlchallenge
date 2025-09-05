# 🥑 Case Study #3 - Foodie-Fi

## 🌮 Solution - B. Data Analysis Questions


### 1. How many customers has Foodie-Fi ever had?

```sql
select count(distinct customer_id) as total_customers
from subscriptions;
```

**Answer:**

<img width="161" height="53" alt="Screenshot 2025-08-15 135542" src="https://github.com/user-attachments/assets/8df28e3e-80f9-4834-8151-769195b5c3ef" />

### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?

```sql
select month(start_date) as subscription_month, count(customer_id) as total_trial_customers
from subscriptions
where plan_id = 0
group by month(start_date)
order by month(start_date);

```

**Answer:**

<img width="322" height="285" alt="Screenshot 2025-08-15 140730" src="https://github.com/user-attachments/assets/b89bc7c6-fb62-4903-8deb-6b41934ee36f" />

### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?

````sql
select p.plan_id, p.plan_name, count(s.customer_id) as no_of_events
from plans p
join subscriptions s
on p.plan_id = s.plan_id
where s.start_date >= '2021-01-01'
group by p.plan_id, p.plan_name
order by p.plan_id;
````

**Answer:**

<img width="309" height="114" alt="Screenshot 2025-08-15 141711" src="https://github.com/user-attachments/assets/126f7b18-2f17-4d57-b67a-d7fc52dc83b3" />


### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

```sql
select count(distinct s.customer_id) as total_customers, 
		round((count(distinct s.customer_id) * 100 / 
		(select count(distinct customer_id) from subscriptions)
		), 1) as percent_of_customers_churned
from subscriptions s
where s.plan_id = 4;
```

**Answer:**

<img width="369" height="52" alt="Screenshot 2025-08-15 153956" src="https://github.com/user-attachments/assets/a006d33f-d0bd-413e-8c62-703c0133e38e" />

### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

```sql
with ranked_cte as (
	select s.customer_id, p.plan_id,
		   row_number() over (
           partition by s.customer_id
           order by s.start_date) as row_num
--            order by s.start_date)
    from subscriptions s
    join plans p
    on s.plan_id = p.plan_id
)

select count(case
		when row_num = 2 and plan_id = 4 then 1
        else 0 end) as churned_customers,
         round( 100 *
			(count(case
			when row_num = 2 and plan_id = 4 then 1
			else 0 end)) 
            / (select (count(distinct customer_id)) from subscriptions), 
            0
            ) as churn_percentage
from ranked_cte
where row_num = 2 and plan_id = 4;

```

**Answer:**

<img width="313" height="52" alt="Screenshot 2025-08-15 162941" src="https://github.com/user-attachments/assets/dc0497b5-1f2b-46a4-b865-605d92e10c72" />

### 6. What is the number and percentage of customer plans after their initial free trial?

```sql
with ranked_cte as (
	select s.customer_id, p.plan_id, p.plan_name,
			row_number() over (
            partition by s.customer_id
            order by s.start_date) as row_num
	from subscriptions s
    join plans p
    on s.plan_id = p.plan_id
)
-- select * from ranked_cte;
select plan_id, plan_name, 
		count(case when row_num != 1 and plan_id != 0
			then 1 else null end) as num_of_paid_plans,    -- null because count also considers 0 while counting
			round( 100 *
			(count(case when row_num != 1 and plan_id != 0
			then 1 else null end)) 
            / (select (count(distinct customer_id)) from subscriptions), 
            1
            ) as plan_percentage

from ranked_cte
group by plan_id, plan_name
order by plan_id;

```

**Answer:**

<img width="458" height="138" alt="Screenshot 2025-08-17 165805" src="https://github.com/user-attachments/assets/ce577caf-5640-4265-a0a6-84fe5d7c9d8f" />

### 7. What is the customer count and percentage breakdown of all plan_name values at 2020-12-31?

```sql
with next_dates as (
	select customer_id, plan_id, start_date,
			lead(start_date) over (
				partition by customer_id
                order by start_date
                ) as next_date
	from subscriptions
    where start_date <= '2020-12-31'
)

select p.plan_id, p.plan_name, 
		count(distinct n.customer_id) as customer_count,
		round(100 * count(distinct n.customer_id) / (select count(distinct customer_id) from subscriptions), 1) as pct_of_customers  -- cannot put AS in subquery over here
from next_dates n
join plans p
on n.plan_id = p.plan_id
where next_date is null
group by p.plan_id, p.plan_name;

```

**Answer:**

<img width="445" height="136" alt="Screenshot 2025-08-17 181520" src="https://github.com/user-attachments/assets/194778a2-5cf5-4294-b1a7-15e0008f4c67" />

### 8. How many customers have upgraded to an annual plan in 2020?

```sql
select count(s.customer_id) as annual_plan_customers
from subscriptions s
join plans p
on s.plan_id = p.plan_id
where p.plan_name = 'pro annual' and start_date like '2020%';
```

**Answer:**

<img width="203" height="62" alt="Screenshot 2025-08-17 181934" src="https://github.com/user-attachments/assets/32aa6ce4-7dce-4a1a-b1cc-673d319a53cb" />

### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

```sql
with trial_plan as (
	select customer_id,
			start_date as trial_date
	from subscriptions
    where plan_id = 0
),
annual_plan as (
	select customer_id,
			start_date as annual_date
	from subscriptions
    where plan_id = 3
)

select avg(datediff(ap.annual_date, tp.trial_date)) as avg_days_till_annual
from trial_plan tp
join annual_plan ap
on tp.customer_id = ap.customer_id;
```

**Answer:**

<img width="189" height="49" alt="Screenshot 2025-08-17 184129" src="https://github.com/user-attachments/assets/70556ab6-a400-43a9-83d9-1b1e6f6d0442" />

### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)?

```sql
with trial_plan as (
	select customer_id,
			start_date as trial_date
	from subscriptions
    where plan_id = 0
),
annual_plan as (
	select customer_id,
			start_date as annual_date
	from subscriptions
    where plan_id = 3
)

select 
		case when datediff(ap.annual_date, tp.trial_date) between 1 and 30 then '1 - 30'
	     when datediff(ap.annual_date, tp.trial_date) between 31 and 60 then '31 - 60'
	     when datediff(ap.annual_date, tp.trial_date) between 61 and 90 then '61 - 90'
	     when datediff(ap.annual_date, tp.trial_date) between 91 and 120 then '91 - 120'
		 when datediff(ap.annual_date, tp.trial_date) between 121 and 150 then '121 - 150'
		 when datediff(ap.annual_date, tp.trial_date) between 151 and 180 then '151 - 180'
		 when datediff(ap.annual_date, tp.trial_date) between 181 and 210 then '181 - 210'
		 when datediff(ap.annual_date, tp.trial_date) between 211 and 240 then '211 - 240'
		 when datediff(ap.annual_date, tp.trial_date) between 241 and 270 then '241 - 270'
		 when datediff(ap.annual_date, tp.trial_date) between 271 and 300 then '271 - 300'
		 when datediff(ap.annual_date, tp.trial_date) between 301 and 330 then '301 - 330'
		 when datediff(ap.annual_date, tp.trial_date) between 331 and 360 then '331 - 360'
	else 'Other'
         end as defined_range,
         count(distinct ap.customer_id) as no_of_customers
from trial_plan tp
join annual_plan ap
on tp.customer_id = ap.customer_id
group by defined_range
order by defined_range;

```

**Answer:**

<img width="272" height="286" alt="Screenshot 2025-08-17 190100" src="https://github.com/user-attachments/assets/311b23e3-5f8f-4f15-be73-d7d85221d748" />

> Here, order by does not work as the values are string.

### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

```sql
with pro_monthly as (
	select customer_id, start_date
    from subscriptions
    where plan_id = 2
),
basic_monthly as (
	select customer_id, start_date
    from subscriptions
    where plan_id = 1
)

select count(bm.customer_id) as customers_from_pm_to_bm
from pro_monthly pm
join basic_monthly bm
on pm.customer_id = bm.customer_id
and bm.start_date > pm.start_date   -- to confirm downgrade
where pm.start_date like '2020%' and bm.start_date like '2020%';

```

**Answer:**

<img width="223" height="51" alt="Screenshot 2025-08-17 191027" src="https://github.com/user-attachments/assets/450ddff6-4a65-4e09-8361-c652664af07d" />
