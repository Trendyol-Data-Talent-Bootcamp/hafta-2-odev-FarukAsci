Soru 2) 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.

```SQL
with medals as(
select athlete, sport, year,
last_value(year) over (partition by athlete, sport  order by year rows between current row and 1 following) as secondMedal,
last_value(year) over (partition by athlete, sport  order by year rows between current row and 2 following) as thirdMedal
from faruk_asci.summer_medals 
where year >= 1980
group by athlete, sport, year
order by athlete, sport, year)
--select * from medals --Yılları görmek için select olarak bu satır kullanılmalı, ancak N defa ard arda madalya alan atletler N-3 defa sonuç olarak veriliyor.
select athlete, sport from medals
where secondMedal = year + 4 and thirdMedal = secondMedal + 4
group by athlete, sport--, year, secondMedal, thirdMedal --İlk yorum kaldırılırsa bu satırdaki group by yorumu kaldırılmalı.
```
