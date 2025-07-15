# :curry: Case Study #1 - Danny's Diner
<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">


## :books: Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Creating dannys_diner Database Schema](#creating-dannys_diner-database-schema)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-1/). 
In some questions, two solutions are listed. Out of which, first one is mine and the latter is from the [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) I referred. 

***

## :briefcase: Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

***

## :bar_chart: Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png)

***

## :file_folder: Creating dannys_diner Database Schema

````sql
create database dannys_diner;
use dannys_diner;

create table sales (
    customer_id varchar(1),
    order_date date,
    product_id int
);

create table menu (
    product_id int unique,
    product_name varchar(20),
    price int
);

create table members (
    customer_id varchar(1) unique,
    join_date date
);

insert into sales
values ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');

insert into menu
values ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  
insert into members
values  ('A', '2021-01-07'),
  ('B', '2021-01-09');
 
````
Using `create table` and `insert into`, we have created the `dannys_diner` schema. It consists of Danny's 3 datasets: `sales`, `menu`, and `members`.

## :sunglasses: Question and Solution

**1. What is the total amount each customer spent at the restaurant?**

````sql
select customer_id, sum(price)
from sales inner join menu
on sales.product_id = menu.product_id
group by customer_id;
````

#### Answer:
| customer_id | sum(price)  |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

**2. How many days has each customer visited the restaurant?**

````sql
select customer_id, count(distinct order_date) as total_visits
from sales
group by customer_id;
````

#### Answer:
| customer_id | total_visits |
| ----------- | -----------  |
| A           | 4            |
| B           | 6            |
| C           | 2            |

**3. What was the first item from the menu purchased by each customer?**

````sql
select distinct customer_id, product_id, order_date
from sales
order by order_date;
````
Note: this does not give only one item.

Solution:
````sql
WITH ordered_sales AS (
  SELECT 
    sales.customer_id, 
    sales.order_date, 
    menu.product_name,
    DENSE_RANK() OVER (
      PARTITION BY sales.customer_id 
      ORDER BY sales.order_date) AS rank
  FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
    ON sales.product_id = menu.product_id
)

SELECT 
  customer_id, 
  product_name
FROM ordered_sales
WHERE rank = 1
GROUP BY customer_id, product_name;
````

#### Answer:
| customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

````sql
select menu.product_name, sales.product_id, count(sales.product_id) as most_frequent
from sales inner join menu
on sales.product_id = menu.product_id
group by sales.product_id
order by most_frequent desc limit 1;
````

#### Answer:
| product_name | most_purchased | 
| ----------- | ----------- |
|    ramen    | 8 |

Solution:
````sql
SELECT 
  menu.product_name,
  COUNT(sales.product_id) AS most_purchased_item
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY menu.product_name
ORDER BY most_purchased_item DESC
LIMIT 1;
````

#### Answer:
| most_purchased | product_name | 
| ----------- | ----------- |
| 8       | ramen |

**5. Which item was the most popular for each customer?**

````sql
select menu.product_name, sales.product_id, count(sales.product_id) as most_frequent
from sales inner join menu
on sales.product_id = menu.product_id
where customer_id = 'A'
group by sales.product_id
order by most_frequent desc limit 1;
````

#### Answer:
| product_name | product_id | most_frequent |
| ----------- | ---------- |------------  |
| curry       | 2          |  2           |


It gives result by changing value in `where` clause but does not consider same count like in B where all items are purchased equally.

Solution:
````sql
WITH most_popular AS (
  SELECT 
    sales.customer_id, 
    menu.product_name, 
    COUNT(menu.product_id) AS order_count,
    DENSE_RANK() OVER (
      PARTITION BY sales.customer_id 
      ORDER BY COUNT(sales.customer_id) DESC) AS rank_
  FROM dannys_diner.menu
  INNER JOIN dannys_diner.sales
    ON menu.product_id = sales.product_id
  GROUP BY sales.customer_id, menu.product_name
)

SELECT 
  customer_id, 
  product_name, 
  order_count
FROM most_popular 
WHERE rank_ = 1;
````

#### Answer:
| customer_id | product_name | order_count |
| ----------- | ---------- |------------  |
| A           | ramen        |  3   |
| B           | sushi        |  2   |
| B           | curry        |  2   |
| B           | ramen        |  2   |
| C           | ramen        |  3   |
*Each user may have more than 1 favourite item.*

**6. Which item was purchased first by the customer after they became a member?**

```sql
select sales.customer_id, sales.order_date, members.join_date, sales.product_id, menu.product_name
from sales left join members 
on sales.customer_id = members.customer_id
left join menu 
on sales.product_id = menu.product_id
where sales.order_date >= members.join_date
order by sales.customer_id;
```

#### Answer:
| customer_id | order_date | join_date    |  product_id    | product_name |
| ----------- | ---------- |------------  | -------------- | ------------ |
| A           | 2021-01-07 |  2021-01-07  |  2             |  curry       |
| A           | 2021-01-10 |  2021-01-07  |  3             |  ramen       |
| A           | 2021-01-11 |  2021-01-07  |  3             |  ramen       |
| A           | 2021-01-11 |  2021-01-07  |  3             |  ramen       |
| B           | 2021-01-11 |  2021-01-09  |  1             |  sushi       |
| B           | 2021-01-16 |  2021-01-09  |  3             |  ramen       |
| B           | 2021-02-01 |  2021-01-09  |  3             |  ramen       |

Here, it fetches all the items purchased.

Solution:
```sql
WITH joined_as_member AS (
  SELECT
    members.customer_id, 
    sales.product_id,
    ROW_NUMBER() OVER (
      PARTITION BY members.customer_id
      ORDER BY sales.order_date) AS row_num
  FROM dannys_diner.members
  INNER JOIN dannys_diner.sales
    ON members.customer_id = sales.customer_id
    AND sales.order_date > members.join_date
)

SELECT 
  customer_id, 
  product_name 
FROM joined_as_member
INNER JOIN dannys_diner.menu
  ON joined_as_member.product_id = menu.product_id
WHERE row_num = 1
ORDER BY customer_id ASC;
````

#### Answer:
| customer_id | product_name |
| ----------- | ---------- |
| A           | ramen        |
| B           | sushi        |

**7. Which item was purchased just before the customer became a member?**

````sql
select sales.customer_id, sales.order_date, members.join_date, sales.product_id, menu.product_name
from sales left join members 
on sales.customer_id = members.customer_id
left join menu 
on sales.product_id = menu.product_id
where sales.order_date < members.join_date
order by sales.customer_id;
````

#### Answer:
| customer_id | order_date | join_date    |  product_id    | product_name |
| ----------- | ---------- |------------  | -------------- | ------------ |
| A           | 2021-01-01 |  2021-01-07  |  1             |  sushi       |
| A           | 2021-01-01 |  2021-01-07  |  2             |  curry       |
| B           | 2021-01-01 |  2021-01-09  |  2             |  curry       |
| B           | 2021-01-02 |  2021-01-09  |  2             |  curry       |
| B           | 2021-01-04 |  2021-01-09  |  1             |  sushi       |

Solution:
````sql
WITH purchased_prior_member AS (
  SELECT 
    members.customer_id, 
    sales.product_id,
    ROW_NUMBER() OVER (
      PARTITION BY members.customer_id
      ORDER BY sales.order_date DESC) AS rank
  FROM dannys_diner.members
  INNER JOIN dannys_diner.sales
    ON members.customer_id = sales.customer_id
    AND sales.order_date < members.join_date
)

SELECT 
  p_member.customer_id, 
  menu.product_name 
FROM purchased_prior_member AS p_member
INNER JOIN dannys_diner.menu
  ON p_member.product_id = menu.product_id
WHERE rank = 1
ORDER BY p_member.customer_id ASC;
````

#### Answer:
| customer_id | product_name |
| ----------- | ---------- |
| A           | sushi        |
| B           | sushi        |

**8. What is the total items and amount spent for each member before they became a member?**

````sql
select sales.customer_id, count(sales.product_id) as total_items, sum(menu.price) as total_amount
from sales left join members 
on sales.customer_id = members.customer_id
left join menu 
on sales.product_id = menu.product_id
where sales.order_date < members.join_date
group by sales.customer_id;
````

#### Answer:
| customer_id | total_items | total_amount |
| ----------- | ---------- |----------  |
| A           | 2 |  25       |
| B           | 3 |  40   |

**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?**

```sql
WITH points_cte AS (
  SELECT 
    menu.product_id, 
    CASE
      WHEN product_id = 1 THEN price * 20
      ELSE price * 10 END AS points
  FROM dannys_diner.menu
)

SELECT 
  sales.customer_id, 
  SUM(points_cte.points) AS total_points
FROM dannys_diner.sales
INNER JOIN points_cte
  ON sales.product_id = points_cte.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id;
````

#### Answer:
| customer_id | total_points | 
| ----------- | ---------- |
| A           | 860 |
| B           | 940 |
| C           | 360 |

**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?**

Solution:
```sql
WITH dates_cte AS (
  SELECT 
    customer_id, 
      join_date, 
      join_date + 6 AS valid_date, 
      DATE_TRUNC(
        'month', '2021-01-31'::DATE)
        + interval '1 month' 
        - interval '1 day' AS last_date
  FROM dannys_diner.members
)

SELECT 
  sales.customer_id, 
  SUM(CASE
    WHEN menu.product_name = 'sushi' THEN 2 * 10 * menu.price
    WHEN sales.order_date BETWEEN dates.join_date AND dates.valid_date THEN 2 * 10 * menu.price
    ELSE 10 * menu.price END) AS points
FROM dannys_diner.sales
INNER JOIN dates_cte AS dates
  ON sales.customer_id = dates.customer_id
  AND dates.join_date <= sales.order_date
  AND sales.order_date <= dates.last_date
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id;
````

#### Answer:
| customer_id | total_points | 
| ----------- | ---------- |
| A           | 1020 |
| B           | 320 |

