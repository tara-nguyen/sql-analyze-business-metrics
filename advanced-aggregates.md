```sql
-- What’s the daily revenue from customers ordering kale smoothies?

select 
  date(ordered_at), round(sum(amount_paid), 2) 
from orders
  join order_items
  on orders.id = order_items.order_id
where name = 'kale-smoothie'
group by 1
order by 1;

-- Calculate the percent of revenue each product represents.

select name, round(sum(amount_paid) / 
  (
    select sum(amount_paid) from order_items
  ) * 100., 2) as pct
from order_items
group by 1
order by 2 desc;

-- Calculate the percent of revenue by category of products.

select
  case name
    when 'kale-smoothie'    then 'smoothie'
    when 'banana-smoothie'  then 'smoothie'
    when 'orange-juice'     then 'drink'
    when 'soda'             then 'drink'
    when 'blt'              then 'sandwich'
    when 'grilled-cheese'   then 'sandwich'
    when 'tikka-masala'     then 'dinner'
    when 'chicken-parm'     then 'dinner'
    else 'other'
  end as category,
  round(100. * sum(amount_paid) / 
    (
      select sum(amount_paid) from order_items
    ), 2) as pct
from order_items
group by 1
order by 2 desc;

-- Count the total orders per product.

select 
  name, count(distinct order_id) as count_order
from order_items
group by 1;

-- Count the number of people making these orders.

select name, 
  count(distinct delivered_to) as count_deliveries
from orders
group by 1;

-- Compute the reorder rate, which is the ratio of the total number of orders to the number of people making those orders.

select name, 
  round(1. * 
    count(distinct order_id) / 
    count(distinct delivered_to), 2) 
    as reorder_rate
from order_items join orders
on orders.id = order_items.order_id
group by 1
order by 2 desc;
```
