Постановка задачи: написать рекурсивный запрос, который выводит таблицу показывающую из каких деталей и какой их вложенности состоят часы до 3 уровня вложенности. Результат должен содержать список названий детелй или других списков 

```
--Создание таблиц
create table details (
	id serial primary key,
	name_detail varchar(200),
	description varchar(200)
);


create table details_compositions(
	id serial primary key,
	id_parent int not null,
	id_child int not null,
	description varchar(100)
);
--Вставка данных
insert into details (name_detail,description) values
('Часы','Обычные настенные часы'),
('Механизм','Внутренний механизм часов, который работает как двигатель и заставляет часы и его функции работать.'),
('Заводная головка',
'Заводная головка в механических часах служит для завода и корректировки времени, а в кварцевых — для остановки часов, корректировки времени, изменения режима.'),
('Блок кварцевого генератора','Блок содержащий кварцевый генератор и элемент питания'),
('Источник питания','Батарейка'),
('Кварцевый генератор','Автогенератор электромагнитных колебаний с колебательной системой, в состав которой входит кварцевый резонатор.'),
('Часовая стрелка','Стрелка показывающая часы'),
('Минутная стрелка','Стрелка показывающая минуты'),
('Секундная стрелка','Стрелка показывающая секунды'),
('Шаговый двигатель','Двигатель для стрелок'),
('Циферблат','Панель часов с цифрами, делениями или другими символами, обозначающими часы, минуты.'),
('Апертура','Небольшое отверствие на циферблате'),
('Корпус','Корпус – это подобие некого контейнера, который защищает хрупкий механизм часов от повреждений.');

--Связи деталей
insert into details_compositions (id_parent,id_child,description) values
(1,13,'Состоит из'),
(13,11,'Содержит'),
(11,12,'Вырезана'),
(11,8,'Воткнута'),
(11,7,'Воткнута'),
(11,9,'Воткнута'),
(13,2,'Механизм'),
(2,4,'Содержит'),
(4,6,'Внедрен'),
(4,5,'Вставлена'),
(2,3,'Содержит'),
(4,10,'Включён');
```

Обход в ширину:
```
with recursive tree(id_parent,id_child,lvl) as (
	select dc.id_parent,dc.id_child,1 as lvl  from details_compositions dc
	left join details_compositions dc2  
		on dc.id_parent =dc2.id_child where dc2.id is null
	union all
	select details_compositions.id_parent,
	details_compositions.id_child, lvl+1 from details_compositions 
	join tree on details_compositions.id_parent  = tree.id_child
)
select	* from tree where lvl <=3 order by lvl;
```

Обход в глубину
```
    
--Обход в глубину
with recursive tree(id_parent,id_child,path) as (
	select dc.id_parent,dc.id_child,array[dc.id_parent]::integer[] from details_compositions dc
	left join details_compositions dc2  
		on dc.id_parent =dc2.id_child where dc2.id is null
	union all
	select details_compositions.id_parent,
	details_compositions.id_child, path || array[details_compositions.id_parent]::integer[] from details_compositions 
	join tree on details_compositions.id_parent  = tree.id_child
)
select	* from tree  order by path;
```

****