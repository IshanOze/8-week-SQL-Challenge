### QUESTIONS AND SOLUTIONS
---
### A. CUSTOMER NODE EXPLORATION
---

#### 1. How many unique nodes are there on the Data Bank system?
~~~~sql
select count(distinct node_id) as nodes
from customer_nodes;
~~~~

Answer
| nodes |
|-------|
| 5     |

---

#### 2. What is the number of nodes per region?
~~~~sql
select region_name, count(distinct node_id) as nodes
from customer_nodes c
join regions r on r.region_id = c.region_id
group by region_name;
~~~~

Answer
| region    | nodes |
|-----------|-------|
| Africa    | 5     |
| America   | 5     |
| Asia      | 5     |
| Australia | 5     |
| Europe    | 5     |

---

#### 3. How many customers are allocated to each region?
~~~~sql
select region_name, count(distinct customer_id) as customers
from customer_nodes c
join regions r on r.region_id = c.region_id
group by region_name;
~~~~

Answer
| region    | customers |
|-----------|-----------|
| Africa    | 102       |
| America   | 105       |
| Asia      | 95        |
| Australia | 110       |
| Europe    | 88        |

---

#### 4. How many days on average are customers reallocated to a different node?
~~~~sql
select round(avg(datediff(end_date, start_date)), 2) as diff
from customer_nodes
where end_date <> '9999-12-31';
~~~~

Answer
| diff  |
|-------|
| 14.63 |

---

#### 5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
#### 95th Percentile
~~~~sql
with cte as (select *,
datediff(end_date, start_date) as diff
from customer_nodes
where end_date <> '9999-12-31')

, perc_cte as(select *,
percent_rank() over(partition by region_id order by diff)*100 as p
from cte)

select region_name, round(avg(diff), 0) as '95th_perc'
from perc_cte p
join regions r on r.region_id = p.region_id
where p > 95
group by region_name;
~~~~

Answer
| region_name | 95th_perc |
|-------------|-----------|
| Australia   | 29        |
| America     | 29        |
| Africa      | 29        |
| Asia        | 29        |
| Europe      | 29        |

#### 80th Percentile
~~~~sql
with cte as (select *, 
datediff(end_date, start_date) as diff
from customer_nodes
where end_date <> '9999-12-31'),

perc_cte as(select *,
percent_rank() over(partition by region_id order by diff)*100 as p
from cte)

select region_name, round(avg(diff), 0) as '80th_perc'
from perc_cte c
join regions r on r.region_id = c.region_id
where p > 80 
group by region_name;
~~~~

Answer
| region_name | 80th_perc |
|-------------|-----------|
| Australia   | 27        |
| America     | 27        |
| Africa      | 27        |
| Asia        | 26        |
| Europe      | 27        |

#### Median
~~~~sql
with cte as (select *, 
datediff(end_date, start_date) as diff
from customer_nodes
where end_date <> '9999-12-31'),

perc_cte as(select *,
percent_rank() over(partition by region_id order by diff)*100 as p
from cte)

select region_name, round(avg(diff), 0) as '80th_perc'
from perc_cte c
join regions r on r.region_id = c.region_id
where p > 50 
group by region_name;
~~~~

Answer
| region_name | 80th_perc |
|-------------|-----------|
| Australia   | 22        |
| America     | 22        |
| Africa      | 23        |
| Asia        | 22        |
| Europe      | 23        |



