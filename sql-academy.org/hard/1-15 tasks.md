### 44
Найдите максимальный возраст (колич. лет) среди обучающихся 10 классов ?

```sql
SELECT
    max(YEAR(now()) - YEAR(birthday)) AS max_year
FROM Student a
JOIN Student_in_class b
    ON a.id = b.student
JOIN Class c
    ON b.class = c.id
WHERE
    name LIKE '10%' 
```



### 45
Какой(ие) кабинет(ы) пользуются самым большим спросом?
```sql
SELECT classroom 
FROM 
(
    SELECT 
        classroom,
        COUNT(*) as count
    FROM Schedule
        GROUP BY classroom
) tmp
WHERE 
    count = (
            SELECT row_number() over(partition by classroom) AS count
            FROM Schedule
            ORDER BY count DESC
            LIMIT 1
            )
```
### 60
Выведите идентификаторы преподавателей, которые хотя бы один раз за всё время преподавали в каждом из одиннадцатых классов.
```sql
SELECT 
    teacher
FROM Schedule l
GROUP by teacher 
HAVING COUNT(DISTINCT class) = (SELECT COUNT(DISTINCT class) FROM Schedule)
```
