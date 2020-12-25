Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var.

```SQL
declare initial timestamp default "2020-03-03 00:00:00";
create or replace table `faruk_asci.active_user_count` (
  `dakika` timestamp,
  `kullanici_sayisi` int64
);

while initial < "2020-03-04 00:00:00" do
  insert into `faruk_asci.active_user_count` (dakika, kullanici_sayisi)
    select initial as tarih, hll_count.extract(hll_count.init(deviceid)) as kullanici_sayisi
    from faruk_asci.pageview
    where view_ts between timestamp_sub(initial, interval 4 minute) and timestamp_add(initial, interval 1 minute);
  set initial = timestamp_add(initial, interval 1 minute);
end while;
select * 
from faruk_asci.active_user_count
order by dakika
```
