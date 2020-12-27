Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var.

```SQL
with hyper_count as(
SELECT * FROM(
SELECT timestamp_trunc(view_ts, minute) minute, HLL_COUNT.init(deviceid) users
FROM faruk_asci.pageview
GROUP BY minute)
ORDER BY minute), 

five_minute_window AS (
  SELECT
    minute,
    ARRAY_AGG(users) OVER (ROWS BETWEEN 4 PRECEDING AND CURRENT ROW) five_minute_users
  FROM hyper_count)
  
SELECT
  minute,(
  SELECT
    HLL_COUNT.merge(users)
  FROM
    UNNEST(five_minute_users) users) user_count
FROM
  five_minute_window
ORDER BY
  minute;
```
Dakikalık distinct userların hll sketchleri hyper_count tablosunda tutulur. Hyper count kullanılarak 5 dakikalık aralıkların sketchleri count çıkarmak üzere aggregate edilir. Bu aggregationlar unnest edilip hll.merge fonksiyonu ile tüm distinct userlar toplanır.
