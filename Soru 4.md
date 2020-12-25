soru 4)

```SQL
create or replace table `dsmbootcamp.faruk_asci.content_category`
as
select * from `dsmbootcamp.sample.content_category`;
```
Yukarıdaki sorgu ile content_category tablosu sıfırlanır.

```SQL
MERGE faruk_asci.content_category t
USING faruk_asci.content_category_20201222_00_59 s
ON T.id = S.id
WHEN MATCHED AND t.category <> s.category THEN
  UPDATE SET t.cdc_date = s.cdc_date,
  t.category = s.category
WHEN NOT MATCHED BY TARGET THEN 
  INSERT ( category, id, is_deleted, cdc_date )
  VALUES (s.category, s.id, s.is_deleted, s.cdc_date)
WHEN NOT MATCHED BY SOURCE THEN
  UPDATE SET t.is_deleted = true;
```
Yukarıdaki sorgu content_category tablosunu günceller.

```SQL
select count(distinct HashValueOfEachRow) from(
select md5(concat(Id, category, is_deleted, cdc_date )) as HashValueOfEachRow from faruk_asci.content_category
union all(select md5(concat(Id, category, is_deleted, cdc_date )) as HashValueOfEachRow from faruk_asci.content_category_target))
```
Yukarıdaki sorgu ile content_category ve content_category_target hashlenip joinlenir, distinct elemanlarının sayısı bulunur.
Bu sayı iki tablonun da distinct elemanları ile eşit olduğu zaman tablolar birbirine eşit demektir.
