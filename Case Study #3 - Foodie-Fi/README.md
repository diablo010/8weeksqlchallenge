# 🥑 Case Study #3: Foodie-Fi

<img src="https://user-images.githubusercontent.com/81607668/129742132-8e13c136-adf2-49c4-9866-dec6be0d30f0.png" width="500" height="520" alt="image">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-3/). I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## :briefcase: Business Task
Danny and his friends launched a new startup Foodie-Fi and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world.

This case study focuses on using subscription style digital data to answer important business questions on customer journey, payments, and business performances.

## :bar_chart: Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/129744449-37b3229b-80b2-4cce-b8e0-707d7f48dcec.png)

## :file_folder: Creating foodie_fi Database Schema
All datasets exist within the foodie_fi database schema - namely `plans`, and `subscriptions`.

```sql
create database foodie_fi;

create table plans (
	plan_id int,
    plan_name varchar(30),
    price int
);

alter table plans
modify column price decimal(5, 2);

insert into plans (plan_id, plan_name, price)
values
	(0, 'trial', 0),
	(1, 'basic monthly', 9.90),
	(2, 'pro monthly', 19.90),
	(3, 'pro annual', 199),
	(4, 'churn', null);

create table subscriptions (
  customer_id int,
  plan_id int,
  start_date date
);

insert into subscriptions (customer_id, plan_id, start_date)
values
  ('1', '0', '2020-08-01'),
  ('1', '1', '2020-08-08'),
  ('2', '0', '2020-09-20'),
  ('2', '3', '2020-09-27'),
  ('3', '0', '2020-01-13'),
  ('3', '1', '2020-01-20');
```
> Get rest of the values of subscription table from [here](https://8weeksqlchallenge.com/case-study-3/).

