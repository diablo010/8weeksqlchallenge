# 🍕 Case Study #2 - Pizza Runner

## 🍝 Solution - A. Pizza Metrics

### 1. How many pizzas were ordered?

```sql
select count(order_id) as total_pizzas
from customer_orders;
```

**Answer:**

<img width="123" height="63" alt="Image" src="https://github.com/user-attachments/assets/fe101ea5-bc25-4af6-9087-9e0d11fb24d7" />

### 2. How many unique customer orders were made?

````sql
select count(distinct order_id) as unique_customer_orders
from customer_orders;
````

**Answer:**

<img width="210" height="60" alt="Image" src="https://github.com/user-attachments/assets/a4cbb1a4-1bff-4be9-b40d-11ce1f8d2e84" />

### 3. How many successful orders were delivered by each runner?

````sql
select count(order_id) as successful_orders
from runner_orders_temp
where pickup_time or distance is not null
group by runner_id;
````

**Answer:**

<img width="237" height="100" alt="Image" src="https://github.com/user-attachments/assets/af670787-2d7c-4bed-b8cb-5c767d550cb1" />

### 4. How many of each type of pizza was delivered?

````sql
select pizza_id, count(pizza_id) as No_of_eachtype_delivered
from customer_orders_temp as c
join runner_orders_temp as r
on c.order_id = r.order_id
where r.distance is not null
group by pizza_id;
````

**Answer:**

<img width="291" height="80" alt="Image" src="https://github.com/user-attachments/assets/dbb79ab1-2849-49c7-998b-d6518e393887" />

### 5. How many Vegetarian and Meatlovers were ordered by each customer?

````sql
select c.customer_id, p.pizza_name, count(c.pizza_id) as pizza_count
from customer_orders_temp as c
left join pizza_names as p
on c.pizza_id = p.pizza_id
group by c.customer_id, p.pizza_name
order by c.customer_id;
````

**Answer:**

<img width="321" height="208" alt="Image" src="https://github.com/user-attachments/assets/34dbb18c-3708-46bd-ba48-31cce80d4b73" />

> Note: All the non-aggregated columns are to be considered while using group by.

### 6. What was the maximum number of pizzas delivered in a single order?

````sql
select c.order_id, count(c.order_id) as max_pizzas_delivered
from customer_orders_temp as c
inner join runner_orders_temp as r
on c.order_id = r.order_id
group by c.order_id
order by max_pizzas_delivered
desc limit 1;
````

**Answer:**

<img width="270" height="58" alt="Image" src="https://github.com/user-attachments/assets/56d03361-2ef9-447d-921a-0c2113bbcf1f" />

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql
select c.customer_id, 
       sum(
            case when c.exclusions is null and c.extras is null then 0
            else 0
            end) as no_change,
	   sum(
            case when c.exclusions is not null or c.extras is not null then 1
            else 0
            end) as at_least_1_change
from customer_orders_temp as c
inner join runner_orders_temp as r
on c.order_id = r.order_id
where distance is not null
group by c.customer_id;
````

**Answer:**

<img width="348" height="132" alt="image" src="https://github.com/user-attachments/assets/2bd31190-827f-4d98-b24d-e97ad1ab42d6" />

### 8. How many pizzas were delivered that had both exclusions and extras?

````sql
select c.customer_id, 
	   sum(
            case when c.exclusions is not null and c.extras is not null then 1
            else 0
            end) as exclusions_and_extras
from customer_orders_temp as c
group by c.customer_id;
````

**Answer:**

<img width="310" height="133" alt="Image" src="https://github.com/user-attachments/assets/1a160161-0fad-4148-808f-2ed514005007" />

### 9. What was the total volume of pizzas ordered for each hour of the day?

````sql
select hour(order_time) as hour_of_day, 
	   count(order_id) as no_of_pizzas
from customer_orders_temp
group by hour(order_time)
order by hour(order_time) asc;
````

**Answer:**

<img width="237" height="158" alt="Image" src="https://github.com/user-attachments/assets/e04c2f9c-1c62-4191-be0e-1f36e2f58a24" />

### 10. What was the volume of orders for each day of the week?

````sql
select dayname(order_time) as day_of_week, 
	   count(order_id) as no_of_pizzas
from customer_orders_temp
group by dayname(order_time)
order by dayname(order_time) asc;  -- order by arranges alphabetically so friday appears first
````

**Answer:**

<img width="240" height="123" alt="Image" src="https://github.com/user-attachments/assets/d8cdff24-262f-4cb8-bfe2-4d89df4375a9" />
