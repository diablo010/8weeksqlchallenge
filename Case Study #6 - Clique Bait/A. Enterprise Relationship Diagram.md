# 🖱️ Case Study #6 - Clique Bait

## 🏢 Solution - A. Enterprise Relationship Diagram

Using the DDL schema details given in [README.md](https://github.com/diablo010/8weeksqlchallenge/blob/main/Case%20Study%20%236%20-%20Clique%20Bait/README.md), create an ERD for all the Clique Bait datasets in [dbdiagram.io](https://dbdiagram.io/home).

```
// Following is the code for clique_bait database schema

Table users {
  user_id integer
  cookie_id varchar(6) [unique]
  start_date timestamp
}

Table events {
  visit_id varchar(6)
  cookie_id varchar(6) [unique]
  page_id integer [unique]
  event_type integer [unique]
  sequence_number integer
  event_time timestamp
}

Table event_identifier {
  event_type integer [unique]
  event_name varchar(13)
}

Table campaign_identifier {
  campaign_id integer
  products varchar(3)
  campaign_name varchar(33)
  start_date timestamp
  end_date timestamp
}

Table page_hierarchy {
  page_id integer [unique]
  page_name varchar(14)
  product_category varchar(9)
  product_id integer
}

// For defining relationships
Ref: users.cookie_id < events.cookie_id
Ref: event_identifier.event_type < events.event_type
Ref: page_hierarchy.page_id < events.page_id
```

Following is the ERD for clique_bait Database Schema:

<img width="968" height="460" alt="Untitled" src="https://github.com/user-attachments/assets/ce162b4e-3467-4b80-8374-39273ff95ce4" />
