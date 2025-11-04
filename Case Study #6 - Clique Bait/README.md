# 🖱️ Case Study #6 - Clique Bait

<img width="500" height="520" alt="image" src="https://github.com/user-attachments/assets/bf06dd61-6e32-4af8-953d-4476e1774fbe" />

## 📚 Table of Contents
- [Business Task](#-business-task)
- [Creating clique_bait Database Schema](#-creating-clique_bait-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-6/).  
I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## 💼 Business Task
Clique Bait is not like your regular online seafood store - the founder and CEO Danny, was also a part of a digital data analytics team and wanted to expand his knowledge into the seafood industry!

In this case study - you are required to support Danny’s vision and analyse his dataset and come up with creative solutions to calculate funnel fallout rates for the Clique Bait online store.

## 📁 Creating clique_bait Database Schema
All datasets exist within the clique_bait database schema - namely `event_identifier`, `campaign_identifier`, `page_hierarchy`, `users` and `events`.

```sql
create schema clique_bait;

create table clique_bait.event_identifier (
	"event_type" integer,
  	"event_name" varchar(15)
);

insert into clique_bait.event_identifier
  ("event_type", "event_name")
values
  ('1', 'Page View'),
  ('2', 'Add to Cart'),
  ('3', 'Purchase'),
  ('4', 'Ad Impression'),
  ('5', 'Ad Click');


create table clique_bait.campaign_identifier (
	"campaign_id" integer,
  	"products" varchar(3),
  	"campaign_name" varchar(33),
  	"start_date" timestamp,
  	"end_date" timestamp
);

insert into clique_bait.campaign_identifier
  ("campaign_id", "products", "campaign_name", "start_date", "end_date")
values
  ('1', '1-3', 'BOGOF - Fishing For Compliments', '2020-01-01', '2020-01-14'),
  ('2', '4-5', '25% Off - Living The Lux Life', '2020-01-15', '2020-01-28'),
  ('3', '6-8', 'Half Off - Treat Your Shellf(ish)', '2020-02-01', '2020-03-31');


create table clique_bait.page_hierarchy (
	"page_id" integer,
  	"page_name" varchar(15),
  	"product_category" varchar(10),
  	"product_id" integer
);

insert into clique_bait.page_hierarchy
	("page_id", "page_name", "product_category", "product_id")
values
	('1', 'Home Page', null, null),
 	('2', 'All Products', null, null),
  	('3', 'Salmon', 'Fish', '1'),
  	('4', 'Kingfish', 'Fish', '2'),
  	('5', 'Tuna', 'Fish', '3'),
  	('6', 'Russian Caviar', 'Luxury', '4'),
  	('7', 'Black Truffle', 'Luxury', '5'),
  	('8', 'Abalone', 'Shellfish', '6'),
  	('9', 'Lobster', 'Shellfish', '7'),
  	('10', 'Crab', 'Shellfish', '8'),
  	('11', 'Oyster', 'Shellfish', '9'),
  	('12', 'Checkout', null, null),
  	('13', 'Confirmation', null, null);


create table clique_bait.users (
	"user_id" integer,
  	"cookie_id" varchar(8),
  	"start_date" timestamp
);

insert into clique_bait.users
  	("user_id", "cookie_id", "start_date")
values
  	('1', 'c4ca42', '2020-02-04'),
  	('2', 'c81e72', '2020-01-18'),
  	('3', 'eccbc8', '2020-02-21'),
    ...,
    ('211', 'a26e03', '2020-02-20'),
  	('64', '87a4ba', '2020-03-18');


create table clique_bait.events (
  	"visit_id" varchar(6),
  	"cookie_id" varchar(6),
  	"page_id" integer,
  	"event_type" integer,
  	"sequence_number" integer,
  	"event_time" timestamp
);

insert into clique_bait.events
  	("visit_id", "cookie_id", "page_id", "event_type", "sequence_number", "event_time")
values
  	('ccf365', 'c4ca42', '1', '1', '1', '2020-02-04 19:16:09.182546'),
  	('ccf365', 'c4ca42', '2', '1', '2', '2020-02-04 19:16:17.358191'),
  	('ccf365', 'c4ca42', '6', '1', '3', '2020-02-04 19:16:58.454669'),
    ...,
    ('355a6a', '87a4ba', '12', '1', '18', '2020-03-18 22:45:54.081818'),
  	('355a6a', '87a4ba', '13', '3', '19', '2020-03-18 22:45:54.984666');
```
> Get rest of the values of users, and events table from [here](https://8weeksqlchallenge.com/case-study-6/).

