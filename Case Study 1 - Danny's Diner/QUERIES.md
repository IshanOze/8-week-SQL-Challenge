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

Answer
| customer_id | total_spent |
|-------------|-------------|
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent $76
- Customer B spent $74
- Customer C spent $36

---

2. How many days has each customer visited the restaurant?
~~~~sql
select customer_id, count(distinct order_date) as days_visited
from sales
group by customer_id;
~~~~

Answer 
| customer_id | days_visited |
|-------------|--------------|
| A           | 4            |
| B           | 6            |
| C           | 2            |

- Customer A visited 4 times
- Customer B visited 6 times
- Customer C visited 2 times

---

3. What was the first item from the menu purchased by each customer?
~~~~sql
with first_item as (
select customer_id, product_name,
dense_rank() over (partition by customer_id order by order_date) as r
from sales s
join menu m on m.product_id = s.product_id)

select distinct customer_id, product_name
from first_item f
where r = 1;
~~~~

Answer
| customer_id | dish  |
|-------------|-------|
| A           | sushi |
| A           | curry |
| B           | curry |
| C           | ramen |

- Customer A ordered both Sushi and Curry for their first order
- Customer B ordered Curry for their first order
- Customer C ordered Ramen for their first order

---

4. What is the most purchased item on the menu and how many times was it purchased by all customers?
~~~~sql
select product_name, count(order_date) as times_ordered
from sales s
join menu m on m.product_id = s.product_id
group by product_name
order by times_ordered desc
limit 1;
~~~~

Answer
| product_name | times_ordered |
|--------------|---------------|
| ramen        | 8             |

- Most ordered Dish is Ramen with 8 orders

---

5. Which item was the most popular for each customer?
~~~~sql
with popular as (select customer_id, product_name, count(s.product_id) as times_ordered, 
dense_rank() over (partition by customer_id order by count(s.customer_id) desc) as r
from sales s
join menu m on s.product_id = m.product_id
group by customer_id, product_name)

select customer_id, product_name, times_ordered
from popular
where r = 1;
~~~~

Answer
| customer_id | product_name | times_ordered |
|-------------|--------------|---------------|
| A           | ramen        | 3             |
| B           | curry        | 2             |
| B           | sushi        | 2             |
| B           | ramen        | 2             |
| C           | ramen        | 3             |

- Ramen is the most popular for Customer A
- Curry, Sushi and Ramen are the most popular for Customer B
- Ramen is the most popular for Customer C

---

6. Which item was purchased first by the customer after they became a member?
~~~~sql
with first_order as (select m.customer_id, product_id,
row_number() over(partition by customer_id order by order_date) as r
from members m
join sales s on s.customer_id = m.customer_id and order_date > join_date)

select customer_id, product_name
from first_order f 
join menu m on m.product_id = f.product_id
where r = 1
order by customer_id;
~~~~

Answer
| customer_id | product_name |
|-------------|--------------|
| A           | ramen        |
| B           | sushi        |

- Customer A ordered Ramen after becoming a member
- Customer B ordered Sushi after becoming a member

---

7. Which item was purchased just before the customer became a member?
~~~~sql
with last_order as (select m.customer_id, product_id, order_date,
row_number() over (partition by m.customer_id order by order_date desc) as r
from members m 
join sales s on s.customer_id = m.customer_id and order_date < join_date)

select customer_id, product_name
from last_order l
join menu m on m.product_id = l.product_id
where r = 1;
~~~~

Answer 
| customer_id | product_name |
|-------------|--------------|
| A           | sushi        |
| B           | sushi        |

- Customer A and Customer B both ordered Sushi just before becoming a member

---

8. What is the total items and amount spent for each member before they became a member?
~~~~sql
select m.customer_id, count(s.customer_id) as total_orders,
sum(price) as total_sales
from members m
join sales s on s.customer_id = m.customer_id and order_date < join_date
join menu on menu.product_id = s.product_id
group by m.customer_id
order by m.customer_id;
~~~~

Answer 
| customer_id | total_orders | total_spent |
|-------------|--------------|-------------|
| A           | 2            | 25          |
| B           | 3            | 40          |

- Customer A spent $25 on 2 products before becoming a member
- Customer B spent $40 on 3 products before becoming a member

---

9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
~~~~sql
with points as (select product_id,
case 
when product_name = 'Sushi' then price * 20
else price * 10
end as points
from menu)

select customer_id, sum(points) as points
from points p
join sales s on s.product_id = p.product_id
group by customer_id;
~~~~

Answer
| customer_id | points |
|-------------|--------|
| A           | 860    |
| B           | 940    |
| C           | 360    |

- Customer A has 860 points
- Customer B has 940 points
- Customer C has 360 points

---

10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
~~~~sql
with cte as (select m.customer_id, join_date, order_date, product_name, price,
date_add(join_date, interval(6) day) as week_ends_on
from members m
join sales s on s.customer_id = m.customer_id
join menu on menu.product_id = s.product_id)

select customer_id, sum(
case 
when product_name='sushi' and order_date < join_date then price*20
when order_date between join_date and week_ends_on then price * 20
else price * 10
end) as points
from cte
where order_date < '2021-02-01'
group by customer_id
order by customer_id;
~~~~

Answer 
| customer_id | points |
|-------------|--------|
| A           | 1370   |
| B           | 820    |

- Customer A has 1370 points at the end of January
- Customer B has 820 points at the end of January
