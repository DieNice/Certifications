---
tags:
  - Аттестация
author: Проскурин Д.А
date: 2024-06-03
---
![](images/Pasted%20image%2020231031214533.png)

# Конспект

В контексте sql под множеством понимают набор данных таблицы (кортежи, строки таблицы). К этим множествам применинмы операции из теории множеств

Объединение

Оператор UNION позволяет объединить два множества (условно две таблицы). Но в отличие от inner/outer join объединения соединяют не столбцы разных таблиц, а два однотипных набора в один. Операция убирает дублирующиеся строки из результата так же, как это делает DISTINCT Формальный синтаксис объединения:
```
SELECT_выражение1
UNION [ALL] SELECT_выражение2
[UNION [ALL] SELECT_выражениеN]
```

Пример:
```
CREATE TABLE Customers
(
    Id SERIAL PRIMARY KEY,
    FirstName VARCHAR(20) NOT NULL,
    LastName VARCHAR(20) NOT NULL,
    AccountSum NUMERIC DEFAULT 0
);
CREATE TABLE Employees
(
    Id SERIAL PRIMARY KEY,
    FirstName VARCHAR(20) NOT NULL,
    LastName VARCHAR(20) NOT NULL
);
  

INSERT INTO Customers(FirstName, LastName, AccountSum) VALUES
('Tom', 'Smith', 2000),
('Sam', 'Brown', 3000),
('Paul', 'Ins', 4200),
('Victor', 'Baya', 2800),
('Mark', 'Adams', 2500),
('Tim', 'Cook', 2800);
  
INSERT INTO Employees(FirstName, LastName) VALUES
('Homer', 'Simpson'),
('Tom', 'Smith'),
('Mark', 'Adams'),
('Nick', 'Svensson');

--Сам запрос на объединение
select Id,FirstName,LastName from customers c 
union
select Id,firstname,lastname from employees e;

--Запрос на объединение без удаления дубликатов
SELECT FirstName, LastName
FROM Customers
UNION ALL SELECT FirstName, LastName 
FROM Employees;
```
При этом названия столбцов объединенной выборки будут совпадать с названия столбцов первой выборки. И если мы захотим при этом еще произвести сортировку, то в выражениях ORDER BY необходимо ориентироваться именно на названия столбцов первой выборки:

ВАЖНО
1. В двух выборках должно быть одинаковое количество стоблцов
2. Столбцы должны соответствовать по типу
3. Можно объединять выборки из одной и той же таблицы
Пример для объединения одной и той же таблицы:
```
SELECT FirstName, LastName, AccountSum + AccountSum * 0.1 AS TotalSum 
FROM Customers WHERE AccountSum < 3000
UNION SELECT FirstName, LastName, AccountSum + AccountSum * 0.3 AS TotalSum 
FROM Customers WHERE AccountSum >= 3000
```


Пересечение

Оператор INTERSECT позволяет найти общие строки для двух выборок, то есть данный оператор выполняет операцию пересечения множеств. Дублирующиеся строки отфильтровываются, если не указано ALL. Для его использования применяется следующий формальный синтаксис:
```
SELECT_выражение1
INTERSECT SELECT_выражение2
```

Пример
```
SELECT FirstName, LastName
FROM Employees
INTERSECT SELECT FirstName, LastName 
FROM Customers;
```


Вычетание

Оператор EXCEPT в PostgreSQL позволяет найти разность двух выборок, то есть те строки которые есть в первой выборке, но которых нет во второй. Дублирующиеся строки отфильтровываются, если не указано ALL.Для его использования применяется следующий формальный синтаксис:
```
SELECT_выражение1
EXCEPT SELECT_выражение2
```

Пример
```
SELECT FirstName, LastName
FROM Customers
EXCEPT SELECT FirstName, LastName 
FROM Employees;
```


Особенности реализации в различных СУБД(pgsql, clickhouse)


Для clickhouse union можно использовать в двух режимах all и distinct
```
SELECT CounterID, 1 AS table, toInt64(count()) AS c
    FROM test.hits
    GROUP BY CounterID

UNION ALL

SELECT CounterID, 2 AS table, sum(Sign) AS c
    FROM test.visits
    GROUP BY CounterID
    HAVING c > 0
```

Ещё одним отличием является то что результирующие столбцы сопоставляются по их индексу (порядку внутри SELECT). Если имена столбцов не совпадают, то имена для конечного результата берутся из первого запроса.

Как и в postgresql в clickhouse при объединении или других операциях со множествами выполняется приведение типов.

Для clickhouse если используете UNION без явного указания UNION ALL или UNION DISTINCT, то вы можете указать режим объединения с помощью настройки union_default_mode, значениями которой могут быть ALL, DISTINCT или пустая строка. Однако если вы используете UNION с настройкой union_default_mode, значением которой является пустая строка, то будет сгенерировано исключение. В следующих примерах продемонстрированы результаты запросов при разных значениях настройки.

```
SET union_default_mode = 'DISTINCT';
SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 2;
```

В clickhouse у intersect и except нет ключевого слова all или distinct, данные выражения по умолчанию могут содержать повторяющиеся строки в то время как в postgresql у union,intersect и except есть ключевое слово all (его отсуствие означает применение distinct) . Но это не накладывает особых ограничений и можно без проблем сделать вот так

```
SELECT 
DISTINCT * FROM (
SELECT column1 [, column2 ]
FROM table1
[WHERE condition]

EXCEPT

SELECT column1 [, column2 ]
FROM table2
[WHERE condition])
```

# Вопросы
- Объединение. Что такое, краткое описание. Пример использования. Ограничения операции;
- Можно ли объединять одну и туже таблицу?
- Как указывать CTE при union? В какой части запроса cte будет видна? Примеры;
- Пересечение. Что такое, краткое описание. Пример использования. Ограничения операции;
- Вычитание. Что такое, краткое описание. Пример использования. Ограничения операции;
- Можно ли заменить операцию вычитанию другими? Пример.
- Особенности работы pgsql, clickhouse;

# Источники

- Официальная документация PostgresPro;