# 🍕 Case Study #2 - Pizza Runner

## 🍝 Solution - C. Ingredient Optimisation

### 1. What are the standard ingredients for each pizza?

````sql
select pt.topping_name
from pizza_names pn
join
pizza_recipes pr
on pn.pizza_id = pr.pizza_id
join pizza_toppings pt
on pr.topping = pt.topping_id
where pr.topping in (
	select topping
    from pizza_recipes
    group by topping
    having count(pizza_id) > 1)
group by pt.topping_name;

````

**Answer:**

<img width="141" height="70" alt="Screenshot 2025-09-05 185917" src="https://github.com/user-attachments/assets/6f4b0b2f-be89-44d1-9af4-97918be6c4c3" />

### 2. What was the most commonly added extra?

````sql
with extras_cte as (
select 
  jt.extra_id,
  count(*) as extra_count
from customer_orders_temp c
join json_table(
  concat('[', replace(c.extras, ' ', ''), ']'),
  "$[*]" columns (extra_id int path "$")
) as jt
on true
group by jt.extra_id
)

select e.extra_id, e.extra_count
from extras_cte e
group by e.extra_id
order by extra_count desc;
````

**Answer:**

<img width="203" height="102" alt="Screenshot 2025-07-19 145353" src="https://github.com/user-attachments/assets/0019e9e4-54b3-439d-9a07-c82a0ddd4c12" />

### 3. What was the most common exclusion?

````sql
with exclusions_cte as (
select 
  jt.exclusion_id,
  count(*) as exclusion_count
from customer_orders_temp c
join json_table(
  concat('[', replace(c.exclusions, ' ', ''), ']'),
  "$[*]" columns (exclusion_id int path "$")
) as jt
on true
group by jt.exclusion_id
)

select e.exclusion_id, e.exclusion_count
from exclusions_cte e
group by e.exclusion_id
order by exclusion_count desc;
````

**Answer:**


<img width="249" height="97" alt="Screenshot 2025-08-13 132413" src="https://github.com/user-attachments/assets/3fa984eb-133e-48c9-9b28-c895c7c31a13" />


### 4. Generate an order item for each record in the customers_orders table in the format of one of the following: Meat Lovers, Meat Lovers - Exclude Beef, Meat Lovers - Extra Bacon, and Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers


````sql
select 
	c.order_id, 
    c.customer_id,
    concat(
		pn.pizza_name,
        case
			when c.exclusions is not null
            then concat(' - Exclude ', 
            (
				 select group_concat(pt.topping_name)
                    from pizza_toppings pt
                    where find_in_set(pt.topping_id, replace(c.exclusions, ' ', ''))
			)
		)
            else ''
		end,
        case
			when c.extras is not null
            then concat(' - Extra ',
            (
				 select group_concat(pt.topping_name)
                    from pizza_toppings pt
                    where find_in_set(pt.topping_id, replace(c.extras, ' ', ''))
			)
		)
            else ''
		end
	) as order_item
from customer_orders_temp c
join pizza_names pn
on c.pizza_id = pn.pizza_id;

````

**Answer:**

<img width="398" height="332" alt="Screenshot 2025-08-13 142022" src="https://github.com/user-attachments/assets/88e2cfad-ffb9-47f0-a1cc-d2d267561741" />

