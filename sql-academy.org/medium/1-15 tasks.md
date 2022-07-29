### 8
В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?

```sql
SELECT 
    town_to,
    TIMEDIFF(time_in, time_out) as flight_time
FROM Trip
WHERE town_from = "Paris" 
```
### 14
Вывести отсортированный по количеству перелетов (по убыванию) и имени (по возрастанию) список пассажиров, совершивших хотя бы 1 полет.
```sql
SELECT 
    name,
    COUNT(*) as count
FROM Pass_in_trip l
JOIN Passenger r
    on l.passenger = r.id
GROUP BY passenger
HAVING count > 0
ORDER BY 
    COUNT(*) desc,
    name
```

### 15
Определить, сколько потратил в 2005 году каждый из членов семьи
```sql
SELECT 
    member_name,
    status,
    sum(unit_price * amount) as costs
FROM FamilyMembers l
JOIN Payments r
    ON l.member_id = r.family_member
WHERE YEAR(date) = 2005
GROUP BY member_name, status 
```
### 20
Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму
```sql
SELECT
    a.status,
    a.member_name,
    sum(unit_price * amount) as costs
FROM FamilyMembers a
JOIN Payments b
    ON a.member_id = b.family_member
JOIN Goods c
    on b.good = c.good_id
JOIN GoodTypes d
    on c.type = d.good_type_id
WHERE
    good_type_name = "entertainment"
GROUP BY 
    a.status,
    a.member_name 
```
### 21
Определить товары, которые покупали более 1 раза
```sql
SELECT DISTINCT good_name 
FROM 
(
    SELECT 
        good_name ,
        ROW_NUMBER() OVER(PARTITION BY good_id) as count
    FROM Payments a
    JOIN Goods b
        ON a.good = b.good_id
) tmp
WHERE count > 1
```
### 26
Определить группы товаров, которые не приобретались в 2005 году
```sql
SELECT 
    l.good_type_name 
FROM GoodTypes l
LEFT JOIN 
(
    SELECT
        good_type_name
    FROM Payments a
    JOIN Goods b
        ON a.good = b.good_id
    JOIN GoodTypes c
        ON b.type = c.good_type_id
    WHERE YEAR(date) = 2005
    GROUP BY good_type_name
) r
ON l.good_type_name = r.good_type_name
WHERE r.good_type_name IS NULL
```
### 27
Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумм
```sql
SELECT
    good_type_name,
    SUM(amount * unit_price) as costs
FROM Payments a
JOIN Goods b
    ON a.good = b.good_id
JOIN GoodTypes c
    ON b.type = c.good_type_id
WHERE YEAR(date) = 2005
GROUP BY good_type_name
```
### 27
Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумм
```sql
SELECT 
    DISTINCT TIMEDIFF
                (
                    (SELECT end_pair FROM Timepair WHERE id = 4),
                    (SELECT start_pair FROM Timepair WHERE id = 2)
                ) as time
FROM Timepair
```
