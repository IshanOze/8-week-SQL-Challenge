### QUESTIONS AND SOLUTIONS

---

### B. Data Analysis Questions

---

#### 1. How many customers has Foodie-Fi ever had?
~~~~sql
select count(distinct customer_id) 
from subscriptions;
~~~~

Answer
| tot_customers |
|---------------|
| 1000          |

- Foodie-Fi has 1000 Customers in total

---

#### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
~~~~sql
select monthname(start_date), sum(
case
	when plan_id = 0 then 1 
    else 0
end) as c
from subscriptions
group by monthname(start_date)
order by c;
~~~~

Answer
| month     | c  |
|-----------|----|
| February  | 68 |
| November  | 75 |
| June      | 79 |
| October   | 79 |
| April     | 81 |
| December  | 84 |
| September | 87 |
| August    | 88 |
| January   | 88 |
| May       | 88 |
| July      | 89 |
| March     | 94 |

- March month saw the most number of downloads of the app

---

#### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
~~~~sql
select plan_id, count(*) as c
from subscriptions
where start_date > '2020-01-01'
group by plan_id
order by plan_id;
~~~~

Answer
| month | c   |
|-------|-----|
| 0     | 997 |
| 1     | 546 |
| 2     | 539 |
| 3     | 258 |
| 4     | 307 |

---

#### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
~~~~sql
select round(sum(
case
	when plan_id = 4 then 1
    else 0
end) / count(distinct customer_id)*100, 2) as c
from subscriptions;
~~~~

Answer
| churn_perc |
|------------|
| 30.70      |

- 30.70% of all customers have been churned

---

#### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
~~~~sql
with cte as(select s1.customer_id
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id 
and datediff(s2.start_date, s1.start_date) = 7
and s2.plan_id = 4)

select round(count(distinct customer_id)/(select count(distinct customer_id) from subscriptions)*100, 2) as churn
from cte;
~~~~

Answer
| churn_perc |
|------------|
| 9.40       |

---

#### 6. What is the number and percentage of customer plans after their initial free trial?
~~~~sql
with cte as (select s2.customer_id, s2.plan_id
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id
and s1.plan_id = 0
and datediff(s2.start_date, s1.start_date) = 7),

cte2 as(select plan_id, count(plan_id) as num
from cte
group by plan_id),

cte3 as (select plan_id, count(plan_id) as den
from subscriptions
group by plan_id)

select cte2.plan_id, num as c, den as total_c, round(num/den*100, 2) as perc
from cte2
join cte3 on cte2.plan_id = cte3.plan_id
order by plan_id;
~~~~

Answer
| plan_id | c   | total_c | perc   |
|---------|-----|---------|--------|
| 1       | 546 | 546     | 100.00 |
| 2       | 325 | 539     | 60.30  |
| 3       | 37  | 258     | 14.34  |
| 4       | 92  | 307     | 29.97  |

---

#### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
~~~~sql
with next_date as (select *,
lead(start_date, 1) over(partition by customer_id order by start_date) as next_date
from subscriptions),

customers_on as(select plan_id, count(distinct customer_id) as customers
from next_date
where next_date is not null and start_date < '2020-12-31' and '2020-12-31' < next_date
or next_date is null and start_date < '2020-12-31'
group by plan_id)

select plan_id, customers, round(customers/(select count(distinct customer_id) from subscriptions)*100, 2) as perc
from customers_on;
~~~~

Answer
| plan_id | customers | perc  |
|---------|-----------|-------|
| 0       | 19        | 1.90  |
| 1       | 224       | 22.40 |
| 2       | 326       | 32.60 |
| 3       | 195       | 19.50 |
| 4       | 235       | 23.50 |

---

#### 8. How many customers have upgraded to an annual plan in 2020?
~~~~sql
select count(distinct customer_id) as customers
from subscriptions
where year(start_date) = 2020 and plan_id = 3;
~~~~

Answer
| customers |
|-----------|
| 195       |

---

#### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
~~~~sql
select round(avg(datediff(s2.start_date, s1.start_date)), 2) as diff
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id
where s1.plan_id = 0 and s2.plan_id = 3;
~~~~

Answer
| diff   |
|--------|
| 104.62 |

---

#### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
~~~~sql
select round(avg(datediff(s2.start_date, s1.start_date)), 2) as diff
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id
where s1.plan_id = 0 and s2.plan_id = 3;

-- Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)

with cte as(select s1.customer_id, datediff(s2.start_date, s1.start_date) as diff
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id
where s1.plan_id = 0 and s2.plan_id = 3)

select 
case
	when diff between 0 and 30 then '0-30'
    when diff between 31 and 60 then '31-60'
    when diff between 61 and 90 then '61-90'
    when diff between 91 and 120 then '91-120'
    when diff between 121 and 150 then '121-150'
    when diff between 151 and 180 then '151-180'
    when diff between 181 and 210 then '181-210'
    when diff between 211 and 240 then '211-240'
    when diff between 241 and 270 then '241-270'
    when diff between 271 and 300 then '271-300'
    when diff > 300 then '>300'
end day_range, count(*) as c
from cte
group by 1
order by c desc;
~~~~

Answer
| day_range | c  |
|-----------|----|
| 0-30      | 49 |
| 121-150   | 42 |
| 151-180   | 36 |
| 91-120    | 35 |
| 61-90     | 34 |
| 181-210   | 26 |
| 31-60     | 24 |
| 241-270   | 5  |
| 211-240   | 4  |
| >300      | 2  |
| 271-300   | 1  |

---

#### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
~~~~sql
select count(*) as c
from subscriptions s1
join subscriptions s2 on s1.customer_id = s2.customer_id
where s1.plan_id = 2 and s2.plan_id = 1;
~~~~

Answer
| c |
|---|
| 0 |

