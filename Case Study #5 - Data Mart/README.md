# 📈 Case Study #5 - Data Mart

<img width="500" height="520" alt="image" src="https://github.com/user-attachments/assets/88d201c8-33fc-4caa-b9b0-3b11a3ed0787" />


## 📚 Table of Contents
- [Business Task](#-business-task)
- [Entity Relationship Diagram](#-entity-relationship-diagram)
- [Creating data_mart Database Schema](#-creating-data_mart-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-5/).  
I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## 💼 Business Task
Data Mart is Danny’s latest venture and after running international operations for his online supermarket that specialises in fresh produce - Danny is asking for your support to analyse his sales performance.

In June 2020 - large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.

Danny needs your help to quantify the impact of this change on the sales performance for Data Mart and it’s separate business areas.

## 📊 Entity Relationship Diagram

<img width="336" height="400" alt="image" src="https://github.com/user-attachments/assets/bfa170ab-ec04-4fe4-bf30-a9dcd2b0ad4f" />

## 📁 Creating data_mart Database Schema
For this case study there is only a single table: `data_mart.weekly_sales`.

```sql
create schema data_mart;
set search_path = data_mart;

drop table if exists data_mart.weekly_sales;
create table data_mart.weekly_sales (
	"week_date" varchar(7),
	"region" varchar(13),
	"platform" varchar(7),
	"segment" varchar(4),
	"customer_type" varchar(8),
	"transactions" integer,
	"sales" integer
);

insert into data_mart.weekly_sales
  ("week_date", "region", "platform", "segment", "customer_type", "transactions", "sales")
values
  ('31/8/20', 'ASIA', 'Retail', 'C3', 'New', '120631', '3656163'),
  ('31/8/20', 'ASIA', 'Retail', 'F1', 'New', '31574', '996575'),
  ('31/8/20', 'USA', 'Retail', 'null', 'Guest', '529151', '16509610'),
   …,
  ('26/3/18', 'EUROPE', 'Retail', 'C4', 'New', '883', '33523'),
  ('26/3/18', 'AFRICA', 'Retail', 'C3', 'Existing', '218516', '12083475');
```
> Get rest of the values of weekly_sales table from [here](https://8weeksqlchallenge.com/case-study-5/).

