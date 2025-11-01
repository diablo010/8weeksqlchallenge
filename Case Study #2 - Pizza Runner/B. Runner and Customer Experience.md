# 🍕 Case Study #2 - Pizza Runner

## 🍝 Solution - B. Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql
select week(registration_date), count(runner_id) as signed_up_runners
from runners
group by week(registration_date);
````

**Answer:**

<img width="350" height="100" alt="Image" src="https://github.com/user-attachments/assets/f0ce6a7c-52a6-4070-817f-1565e9fd8498" />

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql
select r.runner_id, avg(timestampdiff(minute, c.order_time, r.pickup_time)) as avg_time_taken
from customer_orders_temp c
left join runner_orders_temp r
on c.order_id = r.order_id
where r.distance is not null
group by r.runner_id;
````

**Answer:**

<img width="235" height="98" alt="Image" src="https://github.com/user-attachments/assets/fb6e1910-d381-4f1d-af40-bbb33b04bf53" />

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
with prep_time_cte as (
	select c.order_id,
		count(c.order_id) as pizza_order, 
		timestampdiff(minute, min(c.order_time), max(r.pickup_time)) as prep_time
	-- full-group-by prevents due to ordertime and pickuptime
    -- so min max are used
    -- since there is only one value, it gives same value
    from customer_orders_temp c
	left join runner_orders_temp r
	on c.order_id = r.order_id
	where r.distance is not null
	group by c.order_id   -- group by cannot happen on aggregated functions
)

select pizza_order,
       avg(prep_time) as avg_prep_time
from prep_time_cte
group by pizza_order;
````

**Answer:**

<img width="236" height="95" alt="Image" src="https://github.com/user-attachments/assets/dbcef6ae-4969-401f-a0b0-f660481e9695" />

### 4. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql
select c.customer_id, 
	   round(avg(r.distance), 2) as avg_distance
from customer_orders_temp c
left join runner_orders_temp r
on c.order_id = r.order_id
where r.distance is not null
group by c.customer_id;
````

**Answer:**

<img width="234" height="137" alt="Image" src="https://github.com/user-attachments/assets/3eed99c8-e07c-40ca-8bb1-fffe38741de4" />

### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql
select max(r.duration) - min(r.duration) as diff_bw_extreme_delivery_times
from runner_orders_temp r;
````

**Answer:**

<img width="251" height="57" alt="Image" src="https://github.com/user-attachments/assets/0882810e-3ffe-4823-948d-9ee924d11d8f" />

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql
select r.runner_id, 
	   r.order_id, 
       r.distance / r.duration * 60 as speed
from runner_orders_temp r
where r.distance is not null
order by r.runner_id, speed;
````

**Answer:**

<img width="267" height="204" alt="Image" src="https://github.com/user-attachments/assets/361c574d-e7f6-4333-902e-9214f176b9ce" />

### 7. What is the successful delivery percentage for each runner?

````sql
select r.runner_id, 
        100 * sum(
    		case when r.distance is null then 0
                    else 1 end) / count(*) as success_percentage
                    -- if distance is null, then takes 0 value
                    -- or takes 1 for calculation
                    -- count(*) counts null and non-null values
from runner_orders_temp r
group by r.runner_id;
````

**Answer:**

<img width="259" height="95" alt="Image" src="https://github.com/user-attachments/assets/2743fa82-e884-4a5e-883b-02dfe58d6820" />
