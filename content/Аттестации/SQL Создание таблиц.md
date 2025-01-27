---
tags:
  - Аттестация
date: 2024-06-03
author: Проскурин Д.А
---
# Вопросы
1. Создание таблиц, команда создания таблиц. Структура таблицы (поля, колонки, минимальный набор полей). Можно ли создать таблицу без колонок?;
2. Первичные ключи. Какие типы данных могут быть первичными ключами? Что лучше не использовать в качестве первичного ключа, а что предпочтительнее?
3. Автоинкремент. Что такое автоинкремент, зачем нужен? Может быть несколько полей с автоинкрементом в таблице?
4. Есть ли вещественный первичный ключ с автоинкрементом?
5. Внешние ключи. Что такое, зачем нужны? Примеры? Какие типы связей существует?
6. Какие есть варианты при удаление связанных записей?
7. Требования к наименованию таблиц в команде. Требования к наименованию моделей ORM в команде. Требования к описанию полей таблиц.
8. Ограничения. Что такое, зачем нужны? Влияет ли на производительность? (not null, unique, check)
9. Особенности в pgsql, clickhouse;

##  Варианты при удалении связанных записей

1) При удалении или обновление записей, мы можем использовать следующие опции: **`CASCADE`, `SET NULL`, `RESTRICT`, `NO ACTION`, `SET DEFAULT`****.**
- `**CASCADE**`**:** При удалении или обновлении записи в таблице-источнике, все соответствующие записи в таблице-приемнике также будут удалены или обновлены.
- `**SET NULL**`**:** При удалении или обновлении записи в таблице-источнике, значения внешнего ключа в таблице-приемнике будут установлены в **NULL**.
- `**RESTRICT**`**:** Запрещает удаление или обновление записи в таблице-источнике, если в таблице-приемнике есть ссылки на нее.
- `**NO ACTION**`**:** То же самое, что и **`RESTRICT`**.
- `**SET DEFAULT**`**:** Устанавливает значение внешнего ключа в значение по умолчанию, если связанная запись удалена или обновлена.

## Первичные ключи

Основные типы данных, которые могут быть использованы в качестве первичных ключей (**PRIMARY KEY**) или уникальных ключей (**UNIQUE**), включают:
- **INTEGER**: Целочисленный тип данных, который может быть автоматически увеличен при добавлении новых записей.
- **BIGINT**: Большое целое число, которое может быть больше, чем **INTEGER**.
- **SMALLINT**: Маленькое целое число.
- **CHAR(n)** или **VARCHAR(n)**: Символьные типы данных, которые могут хранить строки фиксированной или переменной длины.
- **UUID**: Уникальный идентификатор, который генерируется автоматически и гарантированно уникален в пределах всей таблицы.
- **SERIAL**: Автоматически увеличивающийся целочисленный тип данных, который обычно используется для первичных ключей.
- **BIGSERIAL**: Большой автоматически увеличивающийся целочисленный тип данных.
- **SMALLSERIAL**: Маленький автоматически увеличивающийся целочисленный тип данных.

# Источники

- https://postgrespro.ru/docs/postgrespro/9.5/tutorial-table (Офф. документация Postgres Pro);
- 