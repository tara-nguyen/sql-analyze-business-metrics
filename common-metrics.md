```sql
-- Calculate Daily Active Users for Mineblocks per platform.

select 
  date(created_at), platform,
  count(distinct user_id) as dau
from gameplays
group by 1,2
order by 1,2;

-- Calculate Daily ARPPU (Average Revenue Per Purchasing User), which is defined as the sum of revenue divided by the number of purchasers per day.

select
  date(created_at),
  round(sum(price) / count(distinct user_id), 2) as arppu
from purchases
where refunded_at is null
group by 1
order by 1;

-- Calculate Daily ARPU (Average Revenue Per User), which is defined as revenue divided by the number of players (purchasers or not), per-day.

with daily_revenue as (
  select
    date(created_at) as dt,
    round(sum(price), 2) as rev
  from purchases
  where refunded_at is null
  group by 1
),
daily_players as (
  select
    date(created_at) as dt,
    count(distinct user_id) as players
  from gameplays
  group by 1
)
select
  daily_revenue.dt,
  daily_revenue.rev / daily_players.players
from daily_revenue join daily_players
using (dt);

-- Calculate the 1-day retention, which is defined as the number of players who returned the next day divided by the number of original players, per day.

select 
  date(g1.created_at) as dt, 
  round(100. * 
    count(distinct g2.user_id) /
    count(distinct g1.user_id), 2) as retention
from gameplays g1
left join gameplays g2 on 
  g1.user_id = g2.user_id and 
  date(g1.created_at) = 
    date(datetime(g2.created_at, '-1 day'))
group by 1
order by 1
limit 100;
```
