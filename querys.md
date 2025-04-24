# Запросы


## Создадим новую таблицу 
```sql
CREATE TABLE User (
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    Name TEXT NOT NULL,
    Age INTEGER DEFAULT 0,
    Description TEXT,
    Country VARCHAR(50)
);
```


Заполним её
```sql
INSERT INTO User (Name, Age, Description, Country)
VALUES
    ('John Doe', 25, 'Software engineer', 'USA'),
    ('Jane Smith', 30, 'Marketing manager', 'Canada'),
    ('Bob Johnson', 25, 'Sales representative', 'UK'),
    ('Alice Brown', 28, 'Graphic designer', 'USA'),
    ('Mike Davis', 35, 'Data analyst', 'USA'),
    ('Emily Wilson', 22, 'Web developer', 'Canada'),
    ('Tom Wilson', 25, 'Sales representative', 'Canada'),
    ('Sara Wilson', 22, 'Web developer', 'Canada');
```

---

## Выборка уникальных данных - DISTINCT

В нашей таблице есть несколько стран, тк каждый юзер обладает страной. Давайте получим все уникальные страны

```sql
SELECT DISTINCT Country
FROM User;
```

---

## Фильтрация данных

### `IN`

```sql
SELECT *
FROM User
WHERE Country IN ('USA', 'UK');
```
Такой запрос вернёт только те записи, где страна - USA или UK

```sql
SELECT *
FROM User
WHERE Country NOT IN ('USA', 'UK');
```
Этот запрос вернёт только те записи, где страна не - USA или UK

### `BETWEEN`

Вывод всех возрастов в диапозоне от 25 до 30
```sql
SELECT *
FROM User
WHERE Age BETWEEN 25 AND 30;
```


А тут все, кто не попадает в этот диапозон
```sql
SELECT *
FROM User
WHERE Age NOT BETWEEN 25 AND 30;
```

### `LIKE`

- `%` - любая последовательность символов
- `_` - один символ

Оператор нужен чтобы выводить все записи, которые содержат определенную подстроку

Например, выведем всех, у кого имя начинается на `J`

```sql
SELECT *
FROM User
WHERE Name Like 'J%';
```

или же найдём всех с фамлией на `Wilson`

```sql
SELECT *
FROM User
WHERE Name Like '%Wilson';
```

### `GLOB`

Это почти как оператор `LIKE`, но тут уже используются регулярные выржения

тот же самый прошлый запрос 
```sql
SELECT *
FROM User
WHERE Name GLOB '*Wilson*';
```

---

## Сортировка - ORDER BY

Представим, нам нужно вывести всех от самого младшего к самому старшему
```sql
SELECT *
FROM User
ORDER BY Age;
```


Представим, нам нужно вывести всех от самого старшего к самому младшему
```sql
SELECT *
FROM User
ORDER BY Age DESC;
```

`DESC` - В таком случае сортировка будет происходить в обратном порядке

Так же можно сотировать по псевдониму
```sql
SELECT id, Age * Id as math
FROM User
ORDER BY math ASC;
```
`ASC` - сортировка по умолчанию


Так же есть сортировка по нескольким полям
```sql
SELECT *
FROM User
ORDER BY Age DESC, Name;
```

В начале оно отсортирует от старших к молодым, а потом уже людей с одинаковым возрастом по Алфавиту их имени.


Если у нас есть значения NULL, например
```sql
INSERT INTO User (Name, Age, Country)
VALUES
('John Brown', 25, 'USA');
```

По кмолчанию, он подставляет `NULLS FIRST`, что значит что при сортировки значения NULL буду на самом верху.

Если мы хотим поменять это, можно использовать `NULLS LAST`
```sql
SELECT *
FROM User
ORDER BY Description NULLS LAST;
```

---

## Ограничение данных - LIMIT

Тут всё просто, если хотим вывести только первые N записей, то ничего нас в этом не граничивает, в отличае от SQLite)))))

```sql
SELECT *
FROM User
ORDER BY Age DESC, Name
LIMIT 5;
```
Таким образом мы получим первые 5 записей

Так вторым параметром можно указать сдвиг, например
```sql
SELECT *
FROM User
LIMIT 3, 5;
```
Таки образом можно получить 5 записей, начиная с третей (не включетельно) -

| Id | Name | Age | Description | Country |
| --- | --- | --- | --- | --- |
| 4 | Alice Brown | 28 | Graphic designer | USA |
| 5 | Mike Davis | 35 | Data analyst | USA |
| 6 | Emily Wilson | 22 | Web developer | Canada |
| 7 | Tom Wilson | 25 | Sales representative | Canada |
| 8 | Sara Wilson | 22 | Web developer | Canada |

Альтернатива таким сдвигам - `OFFSET`
```sql
SELECT *
FROM User    
LIMIT 5
OFFSET 3;
```


---

## Агрегатные функции

- `AVG`: вычисляет среднее значение. Есть `DISTINCT`
- `SUM`: вычисляет сумму значений. Есть `DISTINCT`
- `MIN`: вычисляет наименьшее значение
- `MAX`: вычисляет наибольшее значение
- `COUNT`: вычисляет количество строк в запросе. Есть `DISTINCT`

Среднее значение возраста
```sql
SELECT AVG(Age) AS "Среднее значение возраста"
FROM User;
```

С `AVG` можно исользовать `DISTINCT`, тогда будут braться только уникальные значения
```sql
SELECT AVG(DISTINCT Age) AS "Среднее значение уникального возраста"
FROM User;
```

Тут так же работает `WHERE`
```sql
SELECT AVG(Age) AS "Среднее значение возраста"
FROM User
WHERE Age > 25;
```

Можем получить суммы всех ворастов
```sql
SELECT SUM(Age) AS "Сумма возрастов"
FROM User;
```
для уникальных
```sql
SELECT SUM(DISTINCT Age) AS "Сумма уникальных возрастов"
FROM User;
```

`MAX` и `MIN`не учитывают в себе NULL
```sql
SELECT MAX(Age) AS "Максимальный возраст", MIN(Age) AS "Минимальный возраст"
FROM User;
```

`COUNT` не учитывает в себе NULL, если речь идёт о всей строке
```sql
SELECT COUNT(*) AS "Количество людей"
FROM User;
```
Тут ответ - 9

Однако, при запросе 
```sql
SELECT COUNT(Description) AS "Количество описаний"
FROM User;
```
Ответ - 9, тк `NULL` не учитывается

Так же мы можем получить количество уникальных записей при помощи `DISTINCT`
```sql
SELECT COUNT(DISTINCT Description) AS "Количество уникальных описаний"
FROM User;
```
Тут - 6

---

## Группировка данных - GROUP BY

Оператор GROUP BY позволяет группировать данные по одному или нескольким столбцам.

```sql
SELECT Country, COUNT(*) AS user_count
FROM User
GROUP BY Country;
```
Этот запрос покажет нам все уникальные страны и их количество

```sql
SELECT Country, Age, COUNT(*) AS user_count
FROM User
GROUP BY Country, Age;
```

Тут мы группируем по стране и возрасту, тк у нас есть несколько людей с одинаковым страной и возрастом


### Использование с HAVING

`HAVING` позволяет фильтровать результаты группировки. В отличае от `WHERE`, который применяется к всем записям, `HAVING` применяется только к группам.

```sql
SELECT Country, COUNT(*) AS user_count
FROM User
GROUP BY Country
HAVING COUNT(*) > 1;
```

---

## Подзапросы

Подзапросы позволяют выполнять запросы внутри другого запроса.

```sql
SELECT *
FROM User
WHERE Age > (SELECT AVG(Age) FROM User);
```

Тут мы выбираем всех людей, у которых возраст больше среднего возраста всех людей.

Так же есть калеризированные подзапросы, для этого нам нужно создать связную с нашей таблицу

```sql

```

## Коррелирующие подзапросы

Создадим таблицу заказов, которая будет связана с таблицей пользователей:

```sql
CREATE TABLE Orders (
    OrderId INTEGER PRIMARY KEY AUTOINCREMENT,
    UserId INTEGER NOT NULL,
    ProductName TEXT NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    OrderDate DATE NOT NULL,
    FOREIGN KEY (UserId) REFERENCES User(Id)
);
```

Заполним её данными:

```sql
INSERT INTO Orders (UserId, ProductName, Price, OrderDate)
VALUES
    (1, 'Laptop', 1200.00, '2023-01-15'),
    (1, 'Mouse', 25.50, '2023-01-20'),
    (2, 'Keyboard', 85.99, '2023-02-05'),
    (2, 'Monitor', 350.00, '2023-02-10'),
    (3, 'Headphones', 150.00, '2023-02-15'),
    (4, 'Smartphone', 899.99, '2023-03-01'),
    (4, 'Phone Case', 15.00, '2023-03-01'),
    (5, 'Tablet', 450.00, '2023-03-10'),
    (6, 'External Hard Drive', 120.00, '2023-04-05'),
    (7, 'USB Drive', 20.00, '2023-04-10'),
    (1, 'Printer', 200.00, '2023-04-15'),
    (2, 'Webcam', 75.00, '2023-04-20'),
    (3, 'Speaker', 95.00, '2023-05-01'),
    (5, 'Router', 120.00, '2023-05-10');
```

### Пример коррелирующего подзапроса

Коррелирующий подзапрос - это подзапрос, который ссылается на столбцы из внешнего запроса. Он выполняется для каждой строки внешнего запроса.

Пример: Найти всех пользователей и общую сумму их заказов:

```sql
SELECT 
    u.Id,
    u.Name,
    (SELECT SUM(Price) FROM Orders o WHERE o.UserId = u.Id) AS TotalSpent
FROM 
    User u;
```

Другой пример: Найти пользователей, у которых есть заказы дороже 300:

```sql
SELECT 
    *
FROM 
    User u
WHERE (SELECT 1 FROM Orders o WHERE o.UserId = u.Id AND o.Price > 300);
```

Пример с подсчетом количества заказов для каждого пользователя:

```sql
SELECT 
    u.Id,
    u.Name,
    (SELECT COUNT(*) FROM Orders o WHERE o.UserId = u.Id) AS OrderCount
FROM 
    User u;
```

Пример с поиском максимальной стоимости заказа для каждого пользователя:

```sql
SELECT 
    u.Id,
    u.Name,
    (SELECT MAX(Price) FROM Orders o WHERE o.UserId = u.Id) AS MaxOrderPrice
FROM 
    User u;
```