# 🌳 Case Study #7 - Balanced Tree Clothing Co.

<img width="500" height="520" alt="image" src="https://github.com/user-attachments/assets/06c1835a-6d29-40e0-8427-29871a642eef" />

## 📚 Table of Contents
- [Business Task](#-business-task)
- [Entity Relationship Diagram](#-entity-relationship-diagram)
- [Creating balanced_tree Database Schema](#-creating-balanced_tree-database-schema)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-7/).  
I referred this [github repo](https://github.com/katiehuangx/8-Week-SQL-Challenge/tree/main) for some of my solutions. 
***

## 💼 Business Task
Balanced Tree Clothing Company prides themselves on providing an optimised range of clothing and lifestyle wear for the modern adventurer!

Danny, the CEO of this trendy fashion company has asked you to assist the team’s merchandising teams analyse their sales performance and generate a basic financial report to share with the wider business.

## 📊 Entity Relationship Diagram

Using the DDL schema details given [here](https://8weeksqlchallenge.com/case-study-7/), create an ERD for all the Balanced Tree datasets in [dbdiagram.io](https://dbdiagram.io/home).

```
// Following is the code for balanced_tree database schema

Table product_hierarchy {
  id integer
  parent_id integer
  level_text varchar(8)
}

Table product_prices {
  id integer
  product_id varchar(6)
  price integer
}

Table product_details {
  product_id varchar(6)
  price integer
  product_name varchar(32)
  category_id integer
  segment_id integer
  style_id integer
  category_name varchar(6)
  segment_name varchar(6)
  style_name varchar(19)
}

Table sales {
  prod_id varchar(6)
  qty integer
  price integer
  discount integer
  member boolean
  txn_id varchar(6)
  start_txn_time timestamp
}

// For defining relationships
Ref: product_details.product_id < sales.prod_id
Ref: product_details.product_id < product_prices.product_id
Ref: product_hierarchy.parent_id < product_details.category_id
Ref: product_hierarchy.parent_id < product_details.segment_id
Ref: product_hierarchy.parent_id < product_details.style_id
```
Following is the ERD for balanced_tree Database Schema:

<img width="600" height="270" alt="ERD" src="https://github.com/user-attachments/assets/edb0fff4-ce51-4d87-8d5f-04d320467fcb" />

## 📁 Creating balanced_tree Database Schema
For this case study, there are four tables: `product_hierarchy`, `product_prices`, `product_details`, and `sales`.

```sql
create schema balanced_tree;

create table balanced_tree.product_hierarchy (
	"id" integer,
  	"parent_id" integer,
  	"level_text" varchar(19),
  	"level_name" varchar(8)
);


insert into balanced_tree.product_hierarchy
	("id", "parent_id", "level_text", "level_name")
values
  	('1', null, 'Womens', 'Category'),
  	('2', null, 'Mens', 'Category'),
 	('3', '1', 'Jeans', 'Segment'),
    ...,
  	('17', '6', 'White Striped', 'Style'),
  	('18', '6', 'Pink Fluro Polkadot', 'Style');

create table balanced_tree.product_prices (
  	"id" integer,
  	"product_id" varchar(6),
  	"price" integer
);

insert into balanced_tree.product_prices
  	("id", "product_id", "price")
values
  	('7', 'c4a632', '13'),
  	('8', 'e83aa3', '32'),
  	('9', 'e31d39', '10'),
    ...,
	('17', 'b9a74d', '17'),
  	('18', '2feb6b', '29');

create table balanced_tree.product_details (
  	"product_id" varchar(6),
  	"price" integer,
  	"product_name" varchar(32),
  	"category_id" integer,
  	"segment_id" integer,
  	"style_id" integer,
  	"category_name" varchar(6),
  	"segment_name" varchar(6),
  	"style_name" varchar(19)
);

insert into balanced_tree.product_details
  	("product_id", "price", "product_name", "category_id", "segment_id", "style_id", "category_name", "segment_name", "style_name")
values
  	('c4a632', '13', 'Navy Oversized Jeans - Womens', '1', '3', '7', 'Womens', 'Jeans', 'Navy Oversized'),
  	('e83aa3', '32', 'Black Straight Jeans - Womens', '1', '3', '8', 'Womens', 'Jeans', 'Black Straight'),
  	('e31d39', '10', 'Cream Relaxed Jeans - Womens', '1', '3', '9', 'Womens', 'Jeans', 'Cream Relaxed'),
    ...,
  	('b9a74d', '17', 'White Striped Socks - Mens', '2', '6', '17', 'Mens', 'Socks', 'White Striped'),
  	('2feb6b', '29', 'Pink Fluro Polkadot Socks - Mens', '2', '6', '18', 'Mens', 'Socks', 'Pink Fluro Polkadot');


create table balanced_tree.sales (
  	"prod_id" varchar(6),
  	"qty" integer,
  	"price" integer,
  	"discount" integer,
  	"member" boolean,
  	"txn_id" varchar(6),
  	"start_txn_time" timestamp
);

insert into balanced_tree.sales
  	("prod_id", "qty", "price", "discount", "member", "txn_id", "start_txn_time")
values
  	('c4a632', '4', '13', '17', 't', '54f307', '2021-02-13 01:59:43.296'),
  	('5d267b', '4', '40', '17', 't', '54f307', '2021-02-13 01:59:43.296'),
  	('b9a74d', '4', '17', '17', 't', '54f307', '2021-02-13 01:59:43.296'),
    ...,
  	('d5e9a6', '2', '23', '1', 't', '93620b', '2021-03-01 07:11:24.6624'),
  	('5d267b', '2', '40', '1', 't', '93620b', '2021-03-01 07:11:24.6624');
```
> Get rest of the values of tables from [here](https://8weeksqlchallenge.com/case-study-7/).

