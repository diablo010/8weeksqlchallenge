# 🏦 Case Study #4 - Data Bank

<img width="500" height="520" alt="image" src="https://github.com/user-attachments/assets/80610fea-efdc-449c-81e7-8ff591c0a30e" />


## 📚 Table of Contents
- [Business Task](#-business-task)
- [Entity Relationship Diagram](#-entity-relationship-diagram)
- [Creating data_bank Database Schema](#-creating-data_bank-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-4/).  
I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## 💼 Business Task
Danny launched a new initiative, Data Bank which runs banking activities and also acts as the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts.

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!

## 📊 Entity Relationship Diagram

<img width="631" height="342" alt="image" src="https://github.com/user-attachments/assets/d6487383-1d31-4415-b5c7-7062d1c511c8" />

## 📁 Creating data_bank Database Schema
All datasets exist within the data_bank database schema - namely `regions`, `customer_nodes` and `customer_transactions`.

```sql
create schema data_bank;
set search_path = data_bank;

create table data_bank.regions(
	region_id integer,
	region_name text
);

insert into regions
  (region_id, region_name)
values
  ('1', 'Australia'),
  ('2', 'America'),
  ('3', 'Africa'),
  ('4', 'Asia'),
  ('5', 'Europe');

create table customer_nodes (
  customer_id INTEGER,
  region_id INTEGER,
  node_id INTEGER,
  start_date DATE,
  end_date DATE
);

insert into customer_nodes
  (customer_id, region_id, node_id, start_date, end_date)
values
  ('1', '3', '4', '2020-01-02', '2020-01-03'),
  ('2', '3', '5', '2020-01-03', '2020-01-17'),
  ('3', '5', '4', '2020-01-27', '2020-02-18'),
  ('4', '5', '4', '2020-01-07', '2020-01-19'),
  ('5', '3', '3', '2020-01-15', '2020-01-23'),
  ...,
  ('498', '1', '2', '2020-04-05', '9999-12-31'),
  ('499', '5', '1', '2020-02-03', '9999-12-31'),
  ('500', '2', '2', '2020-04-15', '9999-12-31');

create table customer_transactions (
  customer_id integer,
  txn_date date,
  txn_type varchar(10),
  txn_amount integer
);

insert into customer_transactions
  (customer_id, txn_date, txn_type, txn_amount)
values
  ('429', '2020-01-21', 'deposit', '82'),
  ('155', '2020-01-10', 'deposit', '712'),
  ('398', '2020-01-01', 'deposit', '196'),
  ('255', '2020-01-14', 'deposit', '563'),
  ('185', '2020-01-29', 'deposit', '626'),
  ...,
  ('189', '2020-01-22', 'deposit', '302'),
  ('189', '2020-01-27', 'withdrawal', '861');
```
> Get rest of the values of customer_nodes and customer_transactions table from [here](https://8weeksqlchallenge.com/case-study-4/).

