### B. CUSTOMER TRANSACTIONS

---

### QUESTIONS AND SOLUTIONS

---

#### 1. What is the unique count and total amount for each transaction type?
~~~~sql
select txn_type, count(*) as c, sum(txn_amount) as total
from customer_transactions
group by txn_type;
~~~~

Answer
| txn_type   | c    | total   |
|------------|------|---------|
| deposit    | 2671 | 1359168 |
| withdrawal | 1580 | 793003  |
| purchase   | 1617 | 806537  |

---

#### 2. What is the average total historical deposit counts and amounts for all customers?
~~~~sql
select customer_id, count(txn_type) as dep_cnt, sum(txn_amount) as amount
from customer_transactions
where txn_type = 'deposit'
group by customer_id
order by customer_id;
~~~~

Answer
| customer_id | dep_cnt | amount |
|-------------|---------|--------|
| 1           | 2       | 636    |
| 2           | 2       | 610    |
| 3           | 2       | 637    |
| 4           | 2       | 848    |
| 5           | 4       | 2910   |
| 6           | 9       | 4722   |
| 7           | 7       | 4588   |
| 8           | 3       | 2109   |
| 9           | 6       | 3178   |
| 10          | 5       | 2705   |
| 11          | 6       | 2275   |
| 12          | 2       | 1144   |
| 13          | 8       | 3250   |
| 14          | 3       | 1577   |
| 15          | 2       | 1102   |

---

#### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
~~~~sql
with cte as (select customer_id, monthname(txn_date) as m,
sum(if(txn_type='deposit', 1, 0)) as dep_cnt,
sum(if(txn_type='withdrawal', 1, 0)) as withd_cnt,
sum(if(txn_type='purchase', 1, 0)) as purc_cnt
from customer_transactions
group by customer_id, m)

select m, count(customer_id)
from cte
where dep_cnt > 1 and (purc_cnt = 1 or withd_cnt = 1)
group by m;
~~~~

Answer
| month    | customers |
|----------|-----------|
| January  | 115       |
| April    | 50        |
| February | 108       |
| March    | 113       |

---

#### 4. What is the closing balance for each customer at the end of the month?
~~~~sql
with cte as(select customer_id, month(txn_date) as m,
sum(case
	when txn_type='deposit' then txn_amount
    else -txn_amount
end) as net_transaction
from customer_transactions
group by 1, 2
order by 1)

select customer_id, m,
net_transaction,
sum(net_transaction) over(partition by customer_id order by m
							rows between unbounded preceding and current row) as closing_bal
from cte;
~~~~

Answer
| customer_id | month | net_transaction | closing_balance |
|-------------|-------|-----------------|-----------------|
| 1           | 1     | 312             | 312             |
| 1           | 3     | -952            | -640            |
| 2           | 1     | 549             | 549             |
| 2           | 3     | 61              | 610             |
| 3           | 1     | 144             | 144             |
| 3           | 2     | -965            | -821            |
| 3           | 3     | -401            | -1222           |
| 3           | 4     | 493             | -729            |
| 4           | 1     | 848             | 848             |
| 4           | 3     | -193            | 655             |
| 5           | 1     | 954             | 954             |
| 5           | 3     | -2877           | -1923           |

---

