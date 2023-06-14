# CASE STUDY 2 - PIZZA RUNNER
---
### ENTITY RELATAIONSHIP DIAGRAM

![image](https://github.com/IshanOze/8-week-SQL-Challenge/assets/72873175/8206a18d-ae87-41f9-a564-36ac93cf2124)
---

### CLEANING

~~~~sql

#### CREATE NEW TABLE WITH CLEANED VALUES
create table customer_orders as
select order_id, customer_id, pizza_id,
case
	when exclusions is null or exclusions like 'null' then ' '
    else exclusions 
end as exclusions,
case 
	when extras is null or extras like 'null' then ' '
    else extras
end as extras,
order_time 
from temp_customer_orders;


create table runner_orders as 
select order_id, runner_id,
case
	when pickup_time like 'null' then ' '
    else pickup_time
end as pickup_time,
case 
	when distance like 'null' then ' '
	when distance like '%km' then trim('km' from distance)
    else distance
end as distance,
case 
	when duration like 'null' then ' '
	when duration like '%minutes' then trim('minutes' from duration)
    when duration like '%mins' then trim('mins' from duration)
    when duration like '%minute' then trim('minute' from duration)
    else duration
end as duration,
case 
	when cancellation is null or cancellation like 'null' then ' '
    else cancellation
end as cancellation
from temp_runner_orders;
---

#### CHANGE THE COLUMN TYPE OF THE TABLE

-- drop the temp tables
drop table temp_customer_orders;
drop table temp_runner_orders;

-- create a new column to change the datatype of pickup_time
alter table runner_orders
add column new_pickup_time timestamp;


-- set the timestamp value of pickup_time in the new column
update runner_orders
set new_pickup_time = timestamp(pickup_time)
where pickup_time not like ' ';

-- drop the old column pickup_time
alter table runner_orders
drop column pickup_time;

-- cast the column distance as float
update runner_orders
set distance = cast(distance as float)
where distance not like ' ';

-- cast the column duration as float
update runner_orders
set duration = cast(duration as float)
where duration not like ' ';

~~~~
---

### QUESTIONS AND SOLUTIONS



