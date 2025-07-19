# 🍕 Case Study #2 - Pizza Runner

## 🧼 Data Cleaning & Transformation

### 🔨 Table: customer_orders

In the `customer_orders` table below:
- In the `exclusions` column, there are blank spaces and null values. 
- In the `extras` column, there are blank spaces and null values.

<img width="562" height="335" alt="Image" src="https://github.com/user-attachments/assets/38754b24-1d8c-4ab8-b503-fa870a90d165" />


Steps to clean the table:
- Create a temporary table with all the columns
- Remove null string values in `exlusions` and `extras` columns and replace with `null` values.

```sql
create temporary table customer_orders_temp as 
select 
  order_id, customer_id, pizza_id,
  case 
        when exclusions is null or exclusions like 'null' or trim(exclusions) = '' then null
        else exclusions
        end as exclusions,
   case 
        when extras is null or extras like 'null' or trim(extras) = '' then null
        else extras
        end as extras,
   order_time
from pizza_runner.customer_orders;
```

This `customers_orders_temp` table will be used to run the queries.

<img width="560" height="332" alt="Image" src="https://github.com/user-attachments/assets/dfde6ac0-2f8a-4b72-89cb-185a569be111" />


### 🔨 Table: runner_orders

In the `runner_orders` table below:
- In the `exclusions` column, there are missing/ blank spaces ' ' and null values. 
- In the `extras` column, there are missing/ blank spaces ' ' and null values.

<img width="657" height="240" alt="Image" src="https://github.com/user-attachments/assets/ad22560b-d1d6-4c29-8757-70f72f4d53ee" />


Steps to clean the table:
- In `pickup_time` column, remove nulls and replace with `null`.
- In `distance` column, remove "km" and nulls and replace with `null`.
- In `duration` column, remove "minutes", "minute" and nulls and replace with `null`.
- In `cancellation` column, remove NULL and null and and replace with `null`.

```sql
create temporary table runner_orders_temp as 
select 
  order_id, runner_id, 
  case 
        when pickup_time like 'null' then null
        else pickup_time
        end as pickup_time,
  case 
        when distance like 'null' or trim(distance) = '' then null
        when distance like '%km' then trim('km' from distance)
        else distance
        end as distance,
   case 
        when duration like 'null' or trim(duration) = '' then null
		when duration like '%mins' then trim('mins' from duration)
		when duration like '%minutes' then trim('minutes' from duration)
		when duration like '%minute' then trim('minute' from duration)
        else duration
        end as duration,
  case 
        when cancellation is null or cancellation like 'null' or trim(cancellation) = '' then null
        else cancellation
        end as cancellation
from pizza_runner.runner_orders;
```

Then, we alter the `distance`, and `duration` columns to the correct data type.

```sql
alter table runner_orders_temp
modify column distance decimal(5,2),
modify column duration int;
```

This `runner_orders_temp` table will be used to run the queries.

<img width="641" height="241" alt="Image" src="https://github.com/user-attachments/assets/fefe1849-cf87-45c8-9558-da0345f7422e" />
