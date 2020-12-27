Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var.

```SQL
create or replace table faruk_asci.hyper_count as
SELECT * FROM(
SELECT timestamp_trunc(view_ts, minute) minute, HLL_COUNT.init(deviceid) users
FROM faruk_asci.pageview
GROUP BY minute)
ORDER BY minute;
```
Yukarıdaki tablo her dakika için o dakikada işlem yapmış distinct userların sketchlerini içerir.

```SQL
with five_minute_window AS (
  SELECT
    minute,
    ARRAY_AGG(users) OVER (ROWS BETWEEN 4 PRECEDING AND CURRENT ROW) five_minute_users
  FROM faruk_asci.hyper_count)
SELECT
  minute,
  (
  SELECT
    HLL_COUNT.merge(users)
  FROM
    UNNEST(five_minute_users) users) user_count
FROM
  five_minute_window
ORDER BY
  minute;
```
İlk adımda oluşturulan tablo içindeki sketchler 5 dakikalık aralıklar ile toplanır. Count değil sketch üzerinden işlem yapıldığı için bir user 5 dakikalık aralık içerisinde kaç işlem yaparsa yapsın sadece bir defa sayılır.
