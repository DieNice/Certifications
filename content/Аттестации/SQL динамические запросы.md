# Динамический SQL

Текст SQL-команды формируется в момент выполнения 

# Причины использования

- Дополнительная гибкость в приложении
- формирование нескольких конкретных запросов вместо одного универсального для оптимизации
**Цена:**
- Операторы не подготавливаются;
- Возрастает риск внедрения SQL-кода;
- Возрастает сложность сопровождения;

>[!info] В большинстве случаев динамический sql не нужен, можно обойтись статическим

>[!info] Динамический sql еще хорош и оправдан для операций Pivot
# Выполнение динамического запроса

**Оператор EXECUTE:**
- выполняет строковое представление SQL-запроса;
- позволяет использовать параметры;
- переменные PL/pgSQL не становятся неявными параметрами;

**Может использоваться вместо SQL-запроса:**
- сам по себе
- при открытии курсора
- в цикле по запросу
- в предложении RETURN QUERY

Оператор EXECUTE позволяет выполнить SQL-команду, заданную в виде строки.
```postgresql
=>DO$$
DECLARE
	cmd CONSTANT text:= 'CREATE TABLE city_msk(
		name text, architect text, founded integer
	)';
BEGIN
		EXECUTE cmd; -- таблица для исторических зданий Москвы
END;
$$
```

Предложение **INTO** оператора **ECECUTE** позвляет вернуть одну (первую) строку результата в переменную составного типа или несколько скалярных переменных.

Для проверки результата выполнения динамической команды можно использовать команду **GET DIAGNOSTICS** , как в и в случае статических команд.

Результат динамического запроса можно обработать в цикле FOR.
```postgresql
DO$$
DECLARE
	rec record;
BEGIN
	FOR rec in EXECUTE 'SELECT * FROM city_msk ORDER BY founded'
	LOOP
		RAISE NOTICE '%', rec;
	END LOOP;
END;
$$;
```
Этот же пример с использованием курсора
```postgresql
DO$$
DECLARE
	cur refcursor;
	rec record;
BEGIN
	OPEN cur FOR EXECUTE 'select * from city_msk order by founded';
	LOOP
		FETCH cur INTO REC;
		EXIT WHEN NOT FOUND;
		RAISE NOTICE '%', rec;
	END LOOP;
END;
$$
```

Оператор RETURN QUERY для возрата строк из функции также может использовать динамические запросы. Напишем функцию возвращающую все здания архитектора, возможно определенного года. Для этого нам понадобятся параметры:
```postgresql
create function sel_msk(architect text, founded integer default null)
returns setof text
as $$
declare
-- Параметры пронумерованы $1,$2
	cmd text:='select name from city_msk where architect = $1 and ($2 is null or founded = $2)';
begin
	return query
		execute cmd
		using architect, founded; -- указываем значения по порядку
end;
$$ language plpgsql;
```

# Формирование динамической команды

**Подстановка значений параметров**
- предложение using
- гарантируется невозможность внедрения sql-кода

**Экранирование значений**
- идентификаторы: format('%I'), quote_ident
- литералы: format('%L'), quote_litaral, quote_nullable
- внедрение SQL-кода невозможно при правильном использовании

**Обычные строковые фунции:**
- конкатинация и др.
- возможно внедрение SQL-кода

Параметры в предложении USING нельзя использовать для имен объектов (названия таблиц, столбцов и пр.) в динамической команде. Такие идентификаторы необходимо экранировать, чтобы структура запроса не могла изменится.

```postgresql
create or replace function sel_city(
city_code text,
architect text,
founded integer deafult null
)
returns setof text
as $$
declare
	cmd text:='
		select name from %I where architect = %L and (%L is null or founded = %L::integer)';
begin
	return query execute format(
	cmd, 'city' || city_code, architect, founded, founded);
end;
$$ language plpgsql;
```

# Вывод
Динамические команды дают дополнительную гибкость.
Формирование отдельных запросов для разных значений параметров с целью оптимизации.
Не подходят для коротких, частых запросов.
Увеличивают сложность поддержки.

# SQLAlchemy (в разработке)
...
# Ссылки
1. https://www.youtube.com/watch?v=hXqvNW2pdcs&ab_channel=PostgresProfessional
2. https://postgrespro.ru/docs/postgresql/16/ecpg-dynamic
3. https://edu.postgrespro.ru/dev1-9.6/dev1_14_plpgsql_dynamic.pdf