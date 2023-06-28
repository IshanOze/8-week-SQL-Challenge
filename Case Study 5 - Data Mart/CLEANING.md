~~~~sql
create table clean_weekly_sales
( with date_cte as 
	(select *,
	str_to_date(week_date, '%d/%m/%y') as formatted_date
    from weekly_sales) 
select formatted_date as week_date,
extract(week from formatted_date) as week_number,
extract(month from formatted_date) as month_number,
extract(year from formatted_date) as calendar_year,

segment,
case
 when right(segment, 1) = '1' then 'Young Adults'
 when right(segment, 1) = '2' then 'Middle Aged'
 when right(segment, 1) in ('3', '4') then 'Retirees'
else 'unknown'
end as age_band,

case
when left(segment, 1) = 'C' then 'Couple'
when left(segment, 1) = 'F' then 'Families'
else 'unknown'
end as demographic,

round(sales/transactions, 2) as avg_tranasactions,
region, platform, customer_type, sales, transactions
from date_cte);

~~~~
