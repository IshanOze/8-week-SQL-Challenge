### QUESTIONS AND SOLUTIONS
---
### A. PIZZA METRICS

#### 1. How many pizzas were ordered?
~~~~sql
select count(pizza_id)
from customer_orders;
~~~~

Answer
| pizzas_ordered |
|----------------|
| 14             |

- 14 Pizzas are ordered in total

---

#### 2. How many unique customer orders were made?
~~~~sql
select count(distinct order_id) as unique_customer_orders
from customer_orders;
~~~~

Answer
| unique_customer_orders |
|------------------------|
| 10                     |

- There are 10 unique customer orders

---

#### 3. How many successful orders were delivered by each runner?
~~~~sql
select runner_id, count(order_id) as successful_orders
from runner_orders
where distance <> 0
group by runner_id;
~~~~

Answer
| runner_id | successful_orders |
|-----------|-------------------|
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

- Runner with id 1 delivered 4 orders successfully
- Runner with id 2 delivered 3 orders successfully
- Runner with id 3 delivered 1 orders successfully

---

#### 4. How many of each type of pizza was delivered?
~~~~sql
select pizza_name, count(c.order_id)
from customer_orders c
join runner_orders r on r.order_id = c.order_id
join pizza_names p on p.pizza_id = c.pizza_id
where distance <> 0
group by pizza_name;
~~~~

Answer 
| pizza_names | total_orders |
|-------------|--------------|
| Meatlovers  | 9            |
| Vegetarian  | 3            |

- A total of 9 Meatlovers Pizza and 3 Vegetarian Pizza are ordered

---

#### 5. How many Vegetarian and Meatlovers were ordered by each customer?
~~~~sql
select customer_id, pizza_name, count(c.order_id) as total_orders
from customer_orders c
join pizza_names p on p.pizza_id = c.pizza_id
group by customer_id, pizza_name
order by customer_id;
~~~~

Answer
| customer_id | pizza_name | total_orders |
|-------------|------------|--------------|
| 101         | Meatlovers | 2            |
| 101         | Vegetarian | 1            |
| 102         | Meatlovers | 2            |
| 102         | Vegetarian | 1            |
| 103         | Meatlovers | 3            |
| 103         | Vegetarian | 1            |
| 104         | Meatlovers | 3            |
| 105         | Vegetarian | 1            |

- Customer 101 ordered 2 Meatlovers Pizza and 1 Vegetarian Pizza
- Customer 102 ordered 2 Meatlovers Pizza and 1 Vegetarian Pizza
- Customer 103 ordered 3 Meatlovers Pizza and 1 Vegetarian Pizza
- Customer 104 ordered 3 Meatlovers Pizza 
- Customer 105 ordered 1 Vegetarian Pizza

---

#### 6. What was the maximum number of pizzas delivered in a single order?
~~~~sql
select c.order_id, count(pizza_id) as count_of_pizzas
from customer_orders c
join runner_orders r on r.order_id = c.order_id
where distance <> 0
group by order_id
order by count_of_pizzas desc;
~~~~

Answer 
| order_id | count_of_pizzas |
|----------|-----------------|
| 4        | 3               |
| 3        | 2               |
| 10       | 2               |
| 1        | 1               |
| 2        | 1               |
| 5        | 1               |
| 7        | 1               |
| 8        | 1               |

- The maximum number of pizzas delivered in a single order are 3

---

#### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
~~~~sql
select customer_id, sum(
case 
	when exclusions <> 0 or extras <> 0 then 1
    else 0
    end) as atleast_1_change,
sum(
case
	when exclusions = 0 and extras = 0 then 1
    else 0
    end) as no_change
from customer_orders c
join runner_orders r on r.order_id = c.order_id
where distance <> 0
group by customer_id;
~~~~

Answer 
| customer_id | atleast_1_change | no_changes |
|-------------|-------------------|------------|
| 101         | 0                 | 2          |
| 102         | 0                 | 3          |
| 103         | 3                 | 0          |
| 104         | 2                 | 1          |
| 105         | 1                 | 0          |

- Customer with ID 101 had 0 pizzas with atleast 1 change and 2 pizzas with no changes
- Customer with ID 102 had 0 pizzas with atleast 1 change and 3 pizzas with no changes
- Customer with ID 103 had 3 pizzas with atleast 1 change and 0 pizzas with no changes
- Customer with ID 104 had 2 pizzas with atleast 1 change and 1 pizzas with no changes
- Customer with ID 105 had 1 pizzas with atleast 1 change and 0 pizzas with no changes

---

#### 8. How many pizzas were delivered that had both exclusions and extras?
~~~~sql
select customer_id, pizza_name, sum(c.pizza_id) as exclusions_extras_count
from customer_orders c
join runner_orders r on r.order_id = c.order_id
join pizza_names p on p.pizza_id = c.pizza_id
where distance <> 0 and exclusions <> 0 and extras <> 0
group by pizza_name, customer_id;
~~~~

Answer
| customer_id | pizza_name | exclusions_extras_count |
|-------------|------------|-------------------------|
| 104         | Meatlovers | 1                       |

- Customer with ID 104 ordered Meatlovers pizza with both exclusions and extras

---

#### 9. What was the total volume of pizzas ordered for each hour of the day?
~~~~sql
select hour(order_time) as hour_of_day, count(order_id) as volume
from customer_orders
group by hour_of_day
order by hour_of_day;
~~~~

Answer
| hour_of_day | volume |
|-------------|--------|
| 11          | 1      |
| 13          | 3      |
| 18          | 3      |
| 19          | 1      |
| 21          | 3      |
| 23          | 3      |

- Highest volume of pizza is at 1.00pm, 6.00pm, 9.00pm and 11.00pm 

---

#### 10. What was the volume of orders for each day of the week?
~~~~sql
select dayname(order_time) as day_of_week, count(order_id) as volume
from customer_orders
group by day_of_week;
~~~~

Answer 
| day_of_week | volume |
|-------------|--------|
| Wednesday   | 5      |
| Thursday    | 3      |
| Saturday    | 5      |
| Friday      | 1      |

- Wednesday and Saturday experience the highest volume with 5 orders each

