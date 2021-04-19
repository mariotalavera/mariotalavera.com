---
layout: post
title:  "Superset - Multi-Tennant By Row Level Security"
date:   2021-03-21 00:01:00 -0500
categories: superset
published: true
---

This post demonstrates how to use Superset's **ROW_LEVEL_SECURITY** feature to control access to data.

### Enabling ROW_LEVEL_SECURITY in config file

To enable this feature, we must first add the following key and sets its value to true in our config file.

```bash
DEFAULT_FEATURE_FLAGS: Dict[str, bool] = {
    "ROW_LEVEL_SECURITY": True, # <-- Add this
}
```

## Sample User Data

Lets create a scenario in our system where table associates users with video streaming services and the movie genres they like.

```sql
DROP TABLE IF EXISTS user_genre_info;

CREATE TABLE user_genre_info(
  user_name VARCHAR(45) NOT NULL,
  stream VARCHAR(45) NOT NULL,
  unikey varchar(36) NOT NULL,
  genre VARCHAR(45) NOT NULL,
  created_ts TIMESTAMP(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3)
  )

INSERT INTO user_genre_info(user_name,stream,unikey,genre) 
VALUES
  ('mario','netflix','6b1c5d8f-a10c-11eb-9888-0242ac110006','comedy'),
  ('diego','netflix','6b1c5d9b-a10c-11eb-9888-0242ac110006','scifi'),
  ('mario','amazon','6b1c5d9e-a10c-11eb-9888-0242ac110006','comedy'),
  ('mario','netflix','6b1c5da1-a10c-11eb-9888-0242ac110006','romantic'),
  ('diego','amazon','6b1c5da3-a10c-11eb-9888-0242ac110006','action');

SELECT * FROM user_genre_info;
```

For this example, our intent is to restrict access to data based on streaming service.  We want a user to see either service but not both.

## Create A Superset Dataset For User table

To use this data in Superset, we must create a dataset and point it to the table created in the previous step.

<img src="/assets/superset_row_level_security/1_dataset.png" alt="Superset Dataset" width="60%">

## Create Superset Users

For this feature, lets create a user for each streaming service.

<img src="/assets/superset_row_level_security/6_netflix_user.png" alt="Netflix User" width="60%">

**Netflix User**

<img src="/assets/superset_row_level_security/7_amazon_user.png" alt="Amazon User" width="60%">

**Amazon User**

## Create A Role For Each Stream

Row level secuity is enforced via roles in Superset.  Per our intent, lets create a role for each streaming service.

<img src="/assets/superset_row_level_security/2_netflix_role.png" alt="Netflix Role" width="60%">

**Netflix Role**

<img src="/assets/superset_row_level_security/3_amazon_role.png" alt="Amazon Role" width="60%">

**Amazon Role**

## Create A Security Filter For Each Stream

Having both a user and a role for each streaming service, the next step is associating these using a **ROW_SECURITY_LEVEL** Filter in Superset.

<img src="/assets/superset_row_level_security/4_netflix_filter.png" alt="Netflix Filter" width="60%">

**Netflix Filter**

<img src="/assets/superset_row_level_security/5_amazon_filter.png" alt="Amazon Filter" width="60%">

**Amazon Filter**

Configuration done.  We are ready to test our work.

## Create A Chart

Lets create a simple chart that returns all the records in our table.

<img src="/assets/superset_row_level_security/8_user_genre_chart.png" alt="User Genre Chart" width="60%">

## Checking Our Work

Login as each user previously created; notice data is restricted to its own streaming service.

<img src="/assets/superset_row_level_security/9_netflix_login_access.png" alt="Netflix Access" width="60%">

**Netflix Login Access**

<img src="/assets/superset_row_level_security/10_amazon_login_access.png" alt="Amazon Access" width="60%">

**Amazon Login Access**

Great job! Very powerful feature that can be leveraged to control row level access to data.  Thanks for reading.