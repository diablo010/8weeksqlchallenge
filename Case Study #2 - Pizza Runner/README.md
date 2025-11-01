# :pizza: Case Study #2 - Pizza Runner

<img src="https://user-images.githubusercontent.com/81607668/127271856-3c0d5b4a-baab-472c-9e24-3c1e3c3359b2.png" alt="Image" width="500" height="520">

## :books: Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Creating pizza_runner Database Schema](#creating-pizza_runner-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-2/). Out of which, first one is mine and the latter is from the [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) I referred. 

## :briefcase: Business Task
Danny is expanding his new Pizza Empire and at the same time, he wants to Uberize it, so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers. 

## :bar_chart: Entity Relationship Diagram
![Pizza Runner](https://github.com/katiehuangx/8-Week-SQL-Challenge/assets/81607668/78099a4e-4d0e-421f-a560-b72e4321f530)

## :file_folder: Creating pizza_runner Database Schema
All datasets exist within the pizza_runner database schema - namely `runners`, `customer_orders`, `runner_orders`, `pizza_names`, `pizza_recipes`, and `pizza_toppings`.

```sql
create database pizza_runner;

use pizza_runner;
drop table if exists runners;
create table runners (
    runner_id int,
    registration_date date
);

insert into runners (runner_id, registration_date)
values   (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');

drop table if exists customer_orders;
create table customer_orders (
  order_id int,
  customer_id int,
  pizza_id int,
  exclusions varchar(4),
  extras varchar(4),
  order_time datetime
);

insert into customer_orders
(order_id, customer_id, pizza_id, exclusions, extras, order_time)
values
  (1, 101, 1, '', '', '2020-01-01 18:05:02'),
  (2, 101, 1, '', '', '2020-01-01 19:00:52'),
  (3, 102, 1, '', '', '2020-01-02 23:51:23'),
  (3, 102, 2, '', null, '2020-01-02 23:51:23'),
  (4, 103, 1, 4, '', '2020-01-04 13:23:46'),
  (4, 103, 1, 4, '', '2020-01-04 13:23:46'),
  (4, 103, 2, 4, '', '2020-01-04 13:23:46'),
  (5, 104, 1, null, 1, '2020-01-08 21:00:29'),
  (6, 101, 2, null, null, '2020-01-08 21:03:13'),
  (7, 105, 2, null, 1, '2020-01-08 21:20:29'),
  (8, 102, 1, null, null, '2020-01-09 23:54:33'),
  (9, 103, 1, 4, '1, 5', '2020-01-10 11:22:59'),
  (10, 104, 1, null, null, '2020-01-11 18:34:49'),
  (10, 104, 1, '2, 6', '1, 4', '2020-01-11 18:34:49');

drop table if exists runner_orders;
create table runner_orders (
  order_id int,
  runner_id int,
  pickup_time varchar(19),
  distance varchar(7),
  duration varchar(10),
  cancellation varchar(23)
);

insert into runner_orders
  (order_id, runner_id, pickup_time, distance, duration, cancellation)
values
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', null),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', null),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', null),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');

drop table if exists pizza_names;
create table pizza_names (
  pizza_id int,
  pizza_name varchar(40)
);

insert into pizza_names
  (pizza_id, pizza_name)
values
  (1, 'Meatlovers'),
  (2, 'Vegetarian');

drop table if exists pizza_recipes;
create table pizza_recipes (
  pizza_id int,
  toppings varchar(40)
);

insert into pizza_recipes
  (pizza_id, topping)
values
  (1, 1),
  (1, 2),
  (1, 3),
  (1, 4),
  (1, 5),
  (1, 6),
  (1, 8),
  (1, 10),
  (2, 4),
  (2, 6),
  (2, 7),
  (2, 9),
  (2, 11),
  (2, 12);

drop table if exists pizza_toppings;
create table pizza_toppings (
  topping_id int,
  topping_name varchar(40)
);

insert into pizza_toppings
  (topping_id, topping_name)
values
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
```
> The `pizza_recipes` table is altered for easier querying.
