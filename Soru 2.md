#Soru 2 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.

Her satırda o eventin yılına ait madalya kazanan kişiler ve önceki/sonraki yıllarda madalya kazanan atletlerin arrayini tutmaya çalıştım.
Ancak array'ler ile aggragate yapamadığım için çözümüm patladı. Bu sorunun üstesinden gelemezsem farklı bir yaklaşım deneyeceğim.


```SQL
select year, sport, discipline, event, athletes_won_medal,
first_value(athletes_won_medal) over(partition by discipline, event order by (year) rows between 1 preceding and 1 preceding) as last_athletes_won_medal,
first_value(athletes_won_medal) over(partition by discipline, event order by (year) rows between 1 following and 1 following) as next_athletes_won_medal
from(select year, sport, discipline, event,
    ARRAY (
    SELECT IF(silver is not null, silver, "foo") UNION ALL 
    SELECT IF(gold is not null, gold, "foo") UNION ALL 
    SELECT IF(bronz is not null, bronz, "foo")) as athletes_won_medal
    from (
      select year, sport, discipline, event,
      MAX(IF(medal = 'Silver', athlete, NULL)) AS silver,
      MAX(IF(medal = 'Gold', athlete, NULL)) AS gold,
      MAX(IF(medal = 'Bronze', athlete, NULL)) AS bronz
      from can_yazici.summer_medals
      where year >= 1980
      group by sport, discipline, event, year
      )
    )
group by sport, discipline, event, year
```
