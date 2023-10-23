
"""
first_test_db.`таблица 1 sql` - таблица с номерами клиентов и типами клиентов
first_test_db.`таблица 2 sql`  - таблица с номерами клиентов, суммами закупок и регионами
"""



"""топ-3 клиентов типа '3/ SCO' по закупкам в Москве за весь период. вложенный запрос в where """

select 
`Номер клиента`
,sum(`total_sum`) as 'total_sum'
from 
(select 
(`01.01.1920`+ `01.02.1920`) as 'total_sum'
,`Номер клиента`
FROM first_test_db.`таблица 2 sql` AS J
where `Номер клиента` in (SELECT DISTINCT `Номер клиента` 
							FROM first_test_db.`таблица 1 sql` 
							WHERE `Тип клиента` LIKE '%SCO') 
and `Регион` = 'Москва') b
GROUP BY `Номер клиента`
order by total_sum DESC
limit 3


""" case, left join, простые агрегирующие, cast """

select 
`Регион`
,`Тип клиента`
,count(J.`Номер клиента`) as 'Кол-во клиентов'
,case 
	when `Товар`= 1 then 'Яблоки'
	when `Товар`= 2 then 'Апельсины'
	when `Товар`= 3 then 'Груши'
	else NULL
end 'Наименование товара'
,sum(`01.01.1920`) as 'Сумма товара'
,cast(avg(`01.01.1920`) as decimal (10,2)) as 'Средний чек' 
FROM first_test_db.`таблица 2 sql` AS J
left join first_test_db.`таблица 1 sql` as T1 on J.`Номер клиента`= T1.`Номер клиента`
group by
`Регион`
,`Товар`
,`Тип клиента`
order by 1, 2,4,5,3


""" Сводная таблица через case и over() """

select 
`Регион`
,sum(`1/ HoReCa`) '1/ HoReCa'
,sum(`1/ HoReCa`)/`Всего` 'Доля HoReCa от Всего'
,sum(`2/ Trader`) '2/ Trader'
,sum(`3/ SCO`) '3/ SCO'
,`Всего`
from(
select 
`Регион`
,case
	when `Тип клиента` = '1/ HoReCa' then sum(`Сумма товара`) over (partition by `Регион`, `Тип клиента`)
	else NULL
end '1/ HoReCa'
,case
	when `Тип клиента` = '2/ Trader' then sum(`Сумма товара`) over (partition by `Регион`, `Тип клиента`)
	else NULL
end '2/ Trader'
,case
	when `Тип клиента` = '3/ SCO' then sum(`Сумма товара`) over (partition by `Регион`, `Тип клиента`)
    else NULL
end '3/ SCO'
,sum(`Сумма товара`) over (partition by `Регион`) 'Всего'
from 
(select 
`Регион`
,`Тип клиента`
,sum(`01.01.1920`) as 'Сумма товара'
FROM first_test_db.`таблица 2 sql` AS J
left join first_test_db.`таблица 1 sql` as T1 on J.`Номер клиента`= T1.`Номер клиента`
group by
`Регион`
,`Тип клиента`) b
group by `Регион`,`Тип клиента`)c
group by 
`Регион`
,`Всего`


""" WITH CTE , case, over (), union, drop / create table """

drop table if exists TAB1

create table TAB1 as 

with MAIN as 
(select
`Регион`
,`Тип клиента`
,sum(`01.01.1920`) as 'Сумма товара'
FROM first_test_db.`таблица 2 sql` AS J
left join first_test_db.`таблица 1 sql` as T1 on J.`Номер клиента`= T1.`Номер клиента`
group by
`Регион`
,`Тип клиента`)
select 
`Регион`
,`1/ HoReCa`
,`1/ HoReCa`/`Общая сумма закупки по типу клиента` 'Доля от общей суммы'
,`Общая сумма закупки по типу клиента`
from
(select 
`Регион`
,case
	when `Тип клиента` = '1/ HoReCa' then sum(`Сумма товара`) over (partition by `Регион`, `Тип клиента`)
	else NULL
end '1/ HoReCa'
,sum(`Сумма товара`) over (partition by `Тип клиента`) 'Общая сумма закупки по типу клиента'
from MAIN 
group by 
`Регион`
,`Тип клиента`) b
where 
`1/ HoReCa` is not null 

select * from TAB1 

select 
`Регион`
,`1/ HoReCa`
,`Доля от общей суммы`
from tab1 
union 
select 
'Всего' as 'Регион'
,`Общая сумма закупки по типу клиента` as '1/ HoReCa'
,`Общая сумма закупки по типу клиента`/`Общая сумма закупки по типу клиента` as 'Доля от общей суммы'
from tab1 






