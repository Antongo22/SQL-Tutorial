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

## WHERE

Разные команды можно выполнять с `WHERE`








