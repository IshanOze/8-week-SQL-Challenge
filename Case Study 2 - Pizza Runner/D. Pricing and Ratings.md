### QUESTIONS AND SOLUTIONS
---
### D. PRICE AND RATINGS
---

### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
~~~~sql
select pizza_id, 
case
when pizza_id = 1 then 12 * count(pizza_id)
when pizza_id = 2 then 10 * count(pizza_id)
end as price 
from customer_orders c
group by pizza_id;
~~~~

Answer
| pizza_id | total_sales |
|----------|-------------|
| 1        | 120         |
| 2        | 40          |

- Pizza Runner made $160 in total

---

### 5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled - how much money does Pizza Runner have left over after these deliveries?
~~~~sql
with c1 as (select sum(
case
when pizza_id = 1 then 12 
when pizza_id = 2 then 10 
end) as earned
from customer_orders c),

c2 as (select sum(distance*0.3) as spent
from runner_orders)

select earned - spent as earned
from c1, c2;
~~~~

Answer
| earned |
|--------|
| 116.44 |

- Pizza Runner earned $116.44 in total after paying the runners


