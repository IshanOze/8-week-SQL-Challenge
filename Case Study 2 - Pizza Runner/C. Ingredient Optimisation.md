### C. INGEDIENT OPTIMISATION
---
### QUESTIONS AND SOLUTIONS
---
#### 1. What are the standard ingredients for each pizza?
~~~~sql
select pizza_name, topping_name
from pizza_recipes r
join pizza_toppings t on t.topping_id = r.toppings
join pizza_names n on n.pizza_id = r.pizza_id
order by pizza_name;
~~~~

Answer 
| pizza_id   | topping_name |
|------------|--------------|
| Meatlovers | Bacon        |
| Meatlovers | BBQ Sauce    |
| Meatlovers | Beef         |
| Meatlovers | Cheese       |
| Meatlovers | Chicken      |
| Meatlovers | Mushrooms    |
| Meatlovers | Pepperoni    |
| Meatlovers | Salami       |
| Vegetarian | Cheese       |
| Vegetarian | Mushrooms    |
| Vegetarian | Onions       |
| Vegetarian | Peppers      |
| Vegetarian | Tomatoes     |
| Vegetarian | Tomato Sauce |

---

#### 2. What was the most commonly added extra?
~~~~sql
select extras, count(extras) as count
from customer_orders
where extras <> 0
group by extras;
~~~~

Answer 
| extras | count |
|--------|-------|
| 1      | 2     |
| 1, 5   | 1     |
| 1, 4   | 1     |

- Bacon is the most commonly added extra

---

#### 3. What was the most common exclusion?
~~~~sql
select exclusions, count(exclusions) as count
from customer_orders
where exclusions <> 0
group by exclusions;
~~~~

Answer 
| exclusions | count |
|------------|-------|
| 4          | 4     |
| 2, 6       | 1     |

- Cheese is the most commonly excluded item

---

#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:

#####Meat Lovers
~~~~sql
select order_id
from customer_orders
where pizza_id = 1
group by order_id;
~~~~

Answer 
| order_id |
|----------|
| 1        |
| 2        |
| 3        |
| 4        |
| 5        |
| 8        |
| 9        |
| 10       |

##### Meat Lovers - Exclude Beef
~~~~sql
select order_id
from customer_orders
where exclusions = 3 and pizza_id = 1
group by order_id;
~~~~

Answer 
There is no result, indicating that there is no order where beef is excluded

#### Meat Lovers - Extra Bacon
~~~~sql
select order_id
from customer_orders
where pizza_id = 1 and extras = 1
group by order_id;
~~~~

Answer 
| order_id |
|----------|
| 5        |
| 9        |
| 10       |


##### Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
~~~~sql
select order_id
from customer_orders
where pizza_id = 1 and (extras = 6 or extras = 9 or exclusions = 4 or exclusions = 1) 
group by order_id;
~~~~

Answer
| order_id |
|----------|
| 4        |
| 9        |

---



