
- [[#Подготовка тестовой базы данных[^4]|Подготовка тестовой базы данных[^4]]]
- [[#Общие подходы к оптимизации|Общие подходы к оптимизации]]
- [[#Этапы обработки запроса|Этапы обработки запроса]]
	- [[#Этапы обработки запроса#Разбор|Разбор]]
	- [[#Этапы обработки запроса#Переписывание (трансформация)|Переписывание (трансформация)]]
	- [[#Этапы обработки запроса#Планирование (оптимизация)|Планирование (оптимизация)]]
	- [[#Этапы обработки запроса#Выполнение|Выполнение]]
- [[#Простой протокол и этапы обработки запросов|Простой протокол и этапы обработки запросов]]
	- [[#Простой протокол и этапы обработки запросов#JIT-компиляция|JIT-компиляция]]
- [[#Расширенный протокол|Расширенный протокол]]
- [[#Подробнее о планировании|Подробнее о планировании]]
- [[#Оценка стоимости|Оценка стоимости]]
- [[#Последовательное сканирование[^7]|Последовательное сканирование[^7]]]
	- [[#Последовательное сканирование[^7]#Последовательное сканирование (Seq Scan)|Последовательное сканирование (Seq Scan)]]
	- [[#Последовательное сканирование[^7]#Параллельные планы выполнения|Параллельные планы выполнения]]
	- [[#Последовательное сканирование[^7]#Параллельное сканирование (Parallel Seq Scan)|Параллельное сканирование (Parallel Seq Scan)]]
	- [[#Последовательное сканирование[^7]#Краткий вывод|Краткий вывод]]
- [[#Индексный доступ[^8]|Индексный доступ[^8]]]
	- [[#Индексный доступ[^8]#Сканирование только индекса|Сканирование только индекса]]
	- [[#Индексный доступ[^8]#Include-индексы|Include-индексы]]
	- [[#Индексный доступ[^8]#Parallel index only scan|Parallel index only scan]]
	- [[#Индексный доступ[^8]#Краткий вывод|Краткий вывод]]
- [[#Сканирование по битовой карте[^9]|Сканирование по битовой карте[^9]]]
	- [[#Сканирование по битовой карте[^9]#Краткий вывод|Краткий вывод]]
- [[#Система версионирования схем на проекте|Система версионирования схем на проекте]]
- [[#Безопасность в SQL запросах|Безопасность в SQL запросах]]
	- [[#Безопасность в SQL запросах#22.6 Безопасность в SQL запросах|22.6 Безопасность в SQL запросах]]
	- [[#Безопасность в SQL запросах#76.3. Статистика планировщика и безопасность|76.3. Статистика планировщика и безопасность]]
	- [[#Безопасность в SQL запросах#34.3.4. Экранирование строковых значений для включения в SQLкоманды|34.3.4. Экранирование строковых значений для включения в SQLкоманды]]
- [[#Оконные функции|Оконные функции]]
- [[#CTE|CTE]]
- [[#Алгоритмы соединений|Алгоритмы соединений]]
	- [[#Алгоритмы соединений#Слиянием (merge join) [^12]|Слиянием (merge join) [^12]]]
	- [[#Алгоритмы соединений#Сортировки|Сортировки]]
	- [[#Алгоритмы соединений#Соединение хэшированием (Hash join) [^11]|Соединение хэшированием (Hash join) [^11]]]
		- [[#Соединение хэшированием (Hash join) [^11]#Однопроходное соединение|Однопроходное соединение]]
		- [[#Соединение хэшированием (Hash join) [^11]#Двухпроходное соединение|Двухпроходное соединение]]
		- [[#Соединение хэшированием (Hash join) [^11]#Parallel hash join|Parallel hash join]]
	- [[#Алгоритмы соединений#Вложенными циклами (Nested loops) [^10]|Вложенными циклами (Nested loops) [^10]]]
- [[#Статистика [^14]|Статистика [^14]]]
	- [[#Статистика [^14]#Базовая статистика|Базовая статистика]]
	- [[#Статистика [^14]#Расширенная статистика|Расширенная статистика]]
		- [[#Расширенная статистика#Функциональные зависимости между столбцами|Функциональные зависимости между столбцами]]
		- [[#Расширенная статистика#Число уникальных комбинаций значений в столбцах.|Число уникальных комбинаций значений в столбцах.]]
		- [[#Расширенная статистика#Список наиболее частых комбинаций значений|Список наиболее частых комбинаций значений]]
	- [[#Статистика [^14]#Вывод|Вывод]]
- [[#Профилирование[^15]|Профилирование[^15]]]
	- [[#Профилирование[^15]#Инструменты|Инструменты]]
	- [[#Профилирование[^15]#Метрики|Метрики]]
	- [[#Профилирование[^15]#Области локализации|Области локализации]]
- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]|Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Настройка стоимости|Настройка стоимости]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Настройка стоимости ввода/вывода|Настройка стоимости ввода/вывода]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Настройка стоимости времени процессора|Настройка стоимости времени процессора]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Настройка стоимости параллельного выполнения|Настройка стоимости параллельного выполнения]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Настройка памяти|Настройка памяти]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Локализация настроек|Локализация настроек]]
		- [[#Локализация настроек#Пример|Пример]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Схема данных|Схема данных]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Физическое расположение|Физическое расположение]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Порядок соединений|Порядок соединений]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Материализация CTE|Материализация CTE]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Изменение запросов|Изменение запросов]]
	- [[#Сложные запросы, их анализ и оптимизация. Приемы оптимизации[^18]#Вывод|Вывод]]
- [[#Визуализация плана запроса|Визуализация плана запроса]]
- [[#Источники|Источники