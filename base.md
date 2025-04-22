# Основа

---

## Создание - CREATE;

```sql
CREATE TABLE Person
(
	Id INTEGER PRIMARY KEY AUTOINCREMENT, # Id SERIAL PRIMARY KEY -  для Postgres
	Name VARCHAR(30) NOT NULL,
	Second_Name VARCHAR(30) NULL,
	Age INTEGER DEFAULT 0
);
```

### Ограничения
- `PRIMARY KEY` Уникальность + NOT NULL
- `FOREIGN KEY` Внешний ключ
- `NOT NULL` Не может быть пустой
- `UNIQUE `  Позволяет только уникальные значения
- `CHECK` Проверка на условия
- `DEFAULT` Значение по умолчанию 
- `CHECK + LENGTH` Проверка длины текста

Пример с `FOREIGN KEY`
```sql
-- Включаем поддержку FOREIGN KEY (по умолчанию выключено)
PRAGMA foreign_keys = ON;

CREATE TABLE departments (
    id INTEGER PRIMARY KEY,
    name TEXT
);

CREATE TABLE workers (
    id INTEGER PRIMARY KEY,
    name TEXT,
    dept_id INTEGER,
    FOREIGN KEY (dept_id) REFERENCES departments(id)  -- Связь с таблицей departments
);

-- Сначала добавляем отдел
INSERT INTO departments (id, name) VALUES (1, 'IT');

-- Теперь можно добавить сотрудника
INSERT INTO workers (name, dept_id) VALUES ('John', 1);  -- OK

-- Ошибка: такого отдела нет
INSERT INTO workers (name, dept_id) VALUES ('Mike', 99);  -- FOREIGN KEY constraint failed
```



Пример с `CHECK`
```sql
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT,
    salary REAL CHECK (salary > 0)  -- Зарплата должна быть > 0
);

INSERT INTO employees (name, salary) VALUES ('Alice', -100);  
-- Ошибка: CHECK constraint failed
```


Пример с `CHECK + LENGTH`
```sql
CREATE TABLE messages (
    id INTEGER PRIMARY KEY,
    text TEXT CHECK (LENGTH(text) <= 100)  -- Не больше 100 символов
);

INSERT INTO messages (text) VALUES ('Hello!');  -- OK
INSERT INTO messages (text) VALUES (REPEAT('A', 101));  -- Ошибка: CHECK constraint failed
```

---

## Вставка - INSERT 

```sql
INSERT INTO Person (Name, Second_Name, Age)
VALUES
("Anton", "Aleynichenko", 18),
("Artemiy", "Aleynichenko", 13),
("Amelia", "Aleynichenko", 10);
```

--- 

## Выборка - SELECT

#### Вывод всех строк
```sql
SELECT * FROM Person;
```

#### Вывод конкретных столбцов
```sql
SELECT Name, Age FROM Person;
```


#### Вывод сгрупированных данных 

```sql
SELECT Name || ' ' || Second_Name, Age FROM Person;
```
При выводе можно делать и другие операции
```sql
SELECT Name || ' ' || Second_Name, Age * 2 FROM Person;
```
Эта операция выведет возраст всех, умноженный на 2


#### Вывод с псевдонимами

```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age * 2 AS "Возраст вдвойне" 
FROM Person;
```

---

## Условие - WHERE

Разные команды можно выполнять с `WHERE`, пока применим в связке с SELECT
Вот операции, которые можно использовать

### Операции
- `=` Равно
- `!=` или `<>` Не равно
- `<` Меньше чем
- `>` Больше чем
- `<=' Меньше или равно
- `>=` Больше или равно

```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Second_Name = "Aleynichenko";
```
это запрос выведет нам всё из базы, тк в ней все с моеё фамилией.

```
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Second_Name != "Aleynichenko";
```
а тут пусто

Выведем всех, у кого востраст больше 12 
```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Age > 12;
```

А теперь всем, у кого возраст меньшн или равен 13
```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Возраст <= 13;
```




### Логические операции
- `AND` Логическое И
- `OR` Логическое ИЛИ
- `NOT` Логическое НЕТ


Выведем всех с моей фамилией, и возрастом больше 15
```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Возраст > 15
AND
Second_Name = "Aleynichenko";
```
Выведет только меня

Выведем всех с моей фамилией, или возрастом больше 15
```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE Возраст > 15
OR
Second_Name = "Aleynichenko";
```
так выведет уже всех


А теперь вывведем всех, кого не зовут Антон 
```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE NOT Name = "Anton";
```

Можно комбинировать операции. Лучше использовать для этого `()`, чтобы не путаться



### NULL
- `IS NULL` Проверка на NULL
- `IS NOT NULL` Проверка на то, что не NULL


Это бывает полезно, когда в столбце у нас могуть быть `NULL` значения

```sql
SELECT 
Name || ' ' || Second_Name AS "Имя и Фамилия", 
Age AS Возраст
FROM Person
WHERE  Name IS NOT NULL;
```
Тут мы получим все записи, тк у нас нет `NULL` в таблице



---
# Обновление данных - UPDATE

Обновление у всех 
```sql
UPDATE Person
SET Age = Age + 1;
```
Можно обновлять несколько компонентов 
```sql
UPDATE Person
SET Age = Age + 1,
	Second_Name = "Алейниченко";
```

Так же можно обновлять только нкжнкю нам часть данных, используя `WHERE`
```sql
UPDATE Person
SET Age = Age - 2,
	Second_Name = "Aleynichenko"
WHERE 
	Second_Name = "Алейниченко";
```


---
# Обновление таблицы - ALTER 
`ALTER` - очень мощная команда, позволяющая менять уже созданные структуры

Создадим для наглядности другую таблицу 
```sql
CREATE TABLE test_table (
  id INTEGER PRIMARY KEY,
  name TEXT,
  age INTEGER,
  city TEXT
);
```

Заполним данными - 
```sql
INSERT INTO test_table (name, age, city) VALUES
  ('Иван', 25, 'Москва'),
  ('Петр', 30, 'Санкт-Петербург'),
  ('Мария', 28, 'Казань');
```


Добавление столбца NULL 
```sql
ALTER TABLE test_table ADD COLUMN country TEXT;
```

Добавление столбца NOT NULL
```sql
ALTER TABLE test_table ADD COLUMN country2 TEXT NOT NULL;
```
будет работать. если записей в этой таблице ещё не было

Эту проблеу решит значение по умолчанию 
```sql
ALTER TABLE test_table ADD COLUMN country3 TEXT NOT NULL DEFAULT 'Россия';
```


Удаление столбца
```sql
ALTER TABLE test_table DROP COLUMN country;
```


Переименование столбца
```sql
ALTER TABLE test_table RENAME COLUMN country3 TO country;
```

Перееименование таблицы 
```sql
ALTER TABLE test_table RENAME TO new_table;
```



---

# Удаление данных

Для  удаления данных используется команда `DELETE`

Для тестов создадим новую запись - 
```sql
INSERT INTO Person (Name, Second_Name)
VALUES
("test", "test"),
("test2", "test2");
```

Сейчас я добавил 2 тестовые записи, по умолчанию возраст там равен 0.

Если нам нужно удалить какую-то конкретную запись, то тут нам поможет `WHERE`


```sql
DELETE FROM Person 
WHERE Age = 0;
```
 таким убразом я удалил все записи, у которых возраст был 0
 
Так же есть возможность удаления вообще всех записей таблицы 
```sql
DELETE FROM Person;
```



---
# Удаление таблицы
 
 Чтобф удалить таблицу, используем `DROP TABLE`
```sql
DROP TABLE Person;
```
