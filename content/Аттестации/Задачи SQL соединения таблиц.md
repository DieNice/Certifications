
![](images/Pasted%20image%2020231031214714.png)

1 задание результат запроса
```
select
	departament,
	count(id_auto)
from
	empl e
left join auto_old ao on
	e.id = ao.id_emp
group by
	departament ;
```


2 задание результат запроса
```
with t2 as 
(
select
	shop,
	max(period) as last_p
from
	shop t
group by
	(shop))
select
	t.shop,
	t.num
from
	shop t
inner join t2 on
	t."period" = t2.last_p
	and t.shop = t2.shop;
```


3 задание результат запроса
```
begin;

delete
from
	auto_old
where
	auto_old .id_emp in (
	select
		ao.id_emp
	from
		auto_old ao
	left join auto_new an on
		ao.id_emp = an.id_emp
		and ao.id_auto = an.id_auto
	where
		an.id_emp isnull);

insert
	into
	auto_old (id_emp ,
	id_auto)
select
	an.id_emp ,
	an.id_auto
from
	auto_old ao
right join auto_new an on
	ao.id_emp = an.id_emp
	and ao.id_auto = an.id_auto
where
	ao.id_emp isnull;

update
	auto_old
set
	id_auto = sq.id_auto
from
	(
	select
		an.id_emp ,
		an.id_auto
	from
		auto_old ao
	join auto_new an on
		ao.id_emp = an.id_emp
		and ao.id_auto != an.id_auto) sq;

commit;
```


