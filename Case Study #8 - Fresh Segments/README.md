# 🍊 Case Study #8 - Fresh Segments

<img width="500" height="520" alt="image" src="https://github.com/user-attachments/assets/0213f8fa-e42b-458f-a359-0a9294755478" />

## 📚 Table of Contents
- [Business Task](#-business-task)
- [Entity Relationship Diagram](#-entity-relationship-diagram)
- [Creating fresh_segments Database Schema](#-creating-fresh_segments-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-8/).  
I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## 💼 Business Task
Danny created Fresh Segments, a digital marketing agency that helps other businesses analyse trends in online ad click behaviour for their unique customer base.

Clients share their customer lists with the Fresh Segments team who then aggregate interest metrics and generate a single dataset worth of metrics for further analysis.

In particular - the composition and rankings for different interests are provided for each client showing the proportion of their customer list who interacted with online assets related to each interest for each month.

## 📊 Entity Relationship Diagram

Using the DDL schema details given [here](https://8weeksqlchallenge.com/case-study-8/), create an ERD for all the Fresh Segments datasets in [QuickDBD](https://www.quickdatabasediagrams.com).

```
# Following is the code for fresh_segments database schema
interest_map
-
id pk int
interest_name string
interest_summary string
created_at timestamp
last_modified timestamp

interest_metrics
-
_month string
_year string
month_year string
interest_id pk string FK >- interest_map.id
composition float
index_value float
ranking int
percentile_ranking float
```
Following is the ERD for fresh_segments Database Schema:

<img width="485" height="316" alt="ERD" src="https://github.com/user-attachments/assets/799f4581-21ae-4479-b671-7db71a51bb7e" />

## 📁 Creating fresh_segments Database Schema
For this case study, there are two tables: `interest_map`, and `interest_metrics`.

```sql
create schema fresh_segments;

create table fresh_segments.interest_map (
  "id" integer,
  "interest_name" text,
  "interest_summary" text,
  "created_at" timestamp,
  "last_modified" timestamp
);

insert into fresh_segments.interest_map
	("id", "interest_name", "interest_summary", "created_at", "last_modified")
values
	('1', 'Fitness Enthusiasts', 'Consumers using fitness tracking apps and websites.', '2016-05-26 14:57:59', '2018-05-23 11:30:12'),
  	('2', 'Gamers', 'Consumers researching game reviews and cheat codes.', '2016-05-26 14:57:59', '2018-05-23 11:30:12'),
  	('3', 'Car Enthusiasts', 'Readers of automotive news and car reviews.', '2016-05-26 14:57:59', '2018-05-23 11:30:12'),
	...,
	('51120', 'Foot Health Researchers', 'People reading news and advice on preventing and treating foot health issues.', '2019-04-26 18:00:00', '2019-04-29 14:20:04'),
  	('51678', 'Plumbers', 'Professionals reading industry news and researching products and services for plumbers.', '2019-05-06 22:00:00', '2019-05-07 18:50:04');
  
-- update the null values
update fresh_segments.interest_map
set interest_summary = null
where interest_summary = '';

create table fresh_segments.interest_metrics (
  "_month" varchar(4),
  "_year" varchar(4),
  "month_year" varchar(7),
  "interest_id" varchar(5),
  "composition" float,
  "index_value" float,
  "ranking" integer,
  "percentile_ranking" float
);

insert into fresh_segments.interest_metrics
	("_month", "_year", "month_year", "interest_id", "composition", "index_value", "ranking", "percentile_ranking")
values
  	('7', '2018', '07-2018', '32486', '11.89', '6.19', '1', '99.86'),
  	('7', '2018', '07-2018', '6106', '9.93', '5.31', '2', '99.73'),
  	('7', '2018', '07-2018', '18923', '10.85', '5.29', '3', '99.59'),
  	...,
  	('null', 'null', 'null', 'null', '1.51', '0.63', '1193', '0.08'),
  	('null', 'null', 'null', 'null', '1.64', '0.62', '1194', '0');
  
-- update null values
update fresh_segments.interest_metrics
set _month = case when _month = 'null' then null::integer else _month::integer end;

update fresh_segments.interest_metrics
set _year = case when _year = 'null' then null::integer else _year::integer end;

update fresh_segments.interest_metrics
set month_year = null
where month_year = 'null';

update fresh_segments.interest_metrics
set interest_id = null
where interest_id = 'null';
```
> Get rest of the values of tables from [here](https://8weeksqlchallenge.com/case-study-8/).

