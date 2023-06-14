### QUESTIONS AND SOLUTIONS
---
### B. RUNNER AND CUSTOMER EXPERIENCE
---
#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
~~~~sql
select week(registration_date, 5) as week_of_reg, count(runner_id) as registered_count
from runners
group by week_of_reg;
~~~~
(mode is 5 since 1st January 2021 is on Friday)

Answer
| week_of_reg | registered_count |
|-------------|------------------|
| 0           | 2                |
| 1           | 1                |
| 2           | 1                |

- Week 0 had 2 registrations
- Week 1 had 1 registrations
- Week 2 had 1 registrations

---

#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
~~~~sql
with cte as (select c.order_id, runner_id, order_time, new_pickup_time,
timestampdiff(minute, order_time, new_pickup_time) as diff
from runner_orders r
join customer_orders c on c.order_id = r.order_id and distance <> 0)

select runner_id, round(avg(diff), 2) as avg_time
from cte
group by runner_id;
~~~~

Answer 
| runner_id | avg_time |
|-----------|----------|
| 1         | 15.33    |
| 2         | 23.40    |
| 3         | 10.00    |

- Runner with ID 1 took 15.33 minutes on average to pickup the order
- Runner with ID 2 took 23.40 minutes on average to pickup the order
- Runner with ID 3 took 10.00 minutes on average to pickup the order

---

#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
~~~~sql
with cte as (select c.order_id, count(pizza_id) as count_pizza, order_time, new_pickup_time, timestampdiff(minute, order_time, new_pickup_time) as min_to_prep
from customer_orders c
join runner_orders r on r.order_id = c.order_id and distance <> 0
group by c.order_id, order_time, new_pickup_time)

select count_pizza, round(avg(min_to_prep), 2) as min_to_prep
from cte
group by count_pizza;
~~~~

Answer 
| count_of_pizzas | min_to_prep |
|-----------------|-------------|
| 1               | 12.00       |
| 2               | 18.00       |
| 3               | 29.00       |

- 1 pizza takes 12 minutes to be prepared
- An order of 2 pizzas take 18 minutes that is 9 minutes per pizza - being the most efficient
- An order of 3 pizzas take 29 minutes to be prepared that is around 10 minutes per pizza

---

#### 4. What was the average distance travelled for each customer?
~~~~sql
select customer_id, round(avg(distance), 2) as dist_travelled
from customer_orders c
join runner_orders r on r.order_id = c.order_id and distance <> 0
group by customer_id;
~~~~

Answer 
| customer_id | dist_travelled |
|-------------|----------------|
| 101         | 20             |
| 102         | 16.73          |
| 103         | 23.4           |
| 104         | 10             |
| 105         | 25             |

- Customer with ID 105 requires the most distance to be travelled at 25 km
- Customer with ID 104 requires the least distance to be travelled at 10 km

---

#### 5. What was the difference between the longest and shortest delivery times for all orders?
~~~~sql
select max(duration)- min(duration) as diff_betn_duration
from runner_orders
where duration <> 0;
~~~~

Answer
| diff_betn_duration |
|--------------------|
| 30                 |

- Difference between the longest(40 min) and shortest(10 min) delivery times is 30 minutes 

---

#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
~~~~sql
select runner_id,
round(distance/duration * 60, 2) as speed
from runner_orders
where distance <> 0
order by runner_id;
~~~~

Answer
| runner_id | speed |
|-----------|-------|
| 1         | 37.5  |
| 1         | 44.44 |
| 1         | 40.2  |
| 1         | 60    |
| 2         | 35.1  |
| 2         | 60    |
| 2         | 93.6  |
| 3         | 40    |

- All of the runners show increased speed with the increase in number of orders
- Runner with ID 2 shows an increase to 93.6 which surely seems to be a false value

---

#### 7. What is the successful delivery percentage for each runner?
~~~~sql
select runner_id,
round(100*sum(
case 
when distance <> 0 then 1
else 0 end)/count(*), 2) as succ_perc
from runner_orders
group by runner_id;
~~~~

Answer
| runner_id | succ_perc |
|-----------|-----------|
| 1         | 100.00    |
| 2         | 75.00     |
| 3         | 50.00     |

- Runner with ID 1 has 100% success rate
- Runner with ID 2 has 75% success rate
- Runner with ID 3 has 50% success rate
