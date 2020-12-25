# Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.

```SQL
WITH rowTable AS (
    select country, sport, COUNT(1) as medals, ROW_NUMBER() OVER(PARTITION BY sport ORDER BY count(1) DESC) AS rank
    from faruk_asci.summer_medals
    where year >= 1980
    group by country, sport
    order by sport, medals desc)
select * from rowtable
where rank in (1,3,5)
```
