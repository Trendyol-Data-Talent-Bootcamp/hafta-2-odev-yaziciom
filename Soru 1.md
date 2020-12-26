# Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.


```SQL
select 
Country,
Sport,
Total_Medals,
Country_Rank
from(
    select 
    Country, 
    Sport, 
    Total_Medals,
    rank() over(partition by Sport order by Total_Medals desc) as Country_Rank
    from(
        select 
        Country, 
        Sport, 
        COUNT(1) as Total_Medals
        from can_yazici.summer_medals
        where Year >= 1980
        group by Sport, Country
    )
    )
where Country_Rank in (1,3,5)
order by Sport asc
```
