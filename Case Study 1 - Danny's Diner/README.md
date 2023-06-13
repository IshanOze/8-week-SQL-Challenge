# CASE STUDY 1 - DANNY'S DINER 

### ENTITY RELATIONSHIP DIAGRAM
![image](https://github.com/IshanOze/8-week-SQL-Challenge/assets/72873175/e0a1db1a-ce7d-40ff-8456-14396386ab11)


### CASE STUDY QUESTIONS AND SOLUTIONS

1. What is the total amount each customer spent at the restaurant?
~~~~sql
select customer_id, sum(price) as total_sales
from sales s
join menu m on m.product_id = s.product_id
group by customer_id;
~~~~
