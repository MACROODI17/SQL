
<img width="782" alt="task" src="https://user-images.githubusercontent.com/108750881/181903434-8b946565-a8b7-470f-82e0-7e9a000fd560.png">

### Первый способ
```sql
WITH
tmp AS
(
    SELECT
        id_check,
        category,
        SUM(quantity) AS amt
    FROM checks l
    JOIN cat r
        ON l.art = r.art
    WHERE
    category IN ('обувь', 'туризм')
    GROUP BY
        id_check,
        category
    ORDER BY id_check
)
SELECT
    count(id_check) as amount 
FROM tmp
GROUP BY
    id_check
HAVING
    MAX(CASE WHEN category = 'обувь' THEN amt ELSE NULL END) > 2
    AND
    MAX(CASE WHEN category = 'туризм' THEN amt ELSE NULL END) >= 1
```
### Второй способ
```sql
WITH 
tmp AS 
( 
    SELECT 
        id_check, 
        category,
        quantity
    FROM checks l 
    JOIN cat r 
        ON l.art = r.art 
    WHERE 
    CATEGORY in ('обувь', 'туризм')  
) 

SELECT
    subquery.*
FROM
(
    SELECT
        id_check, 
        SUM(SUM(CASE category WHEN 'обувь' THEN quantity else 0 end))  Over (Partition by id_check) AS shoes_amt, 
        SUM(SUM(CASE category WNEN 'туризм' THEN quantity else 0 end)) Over (Partition by id_check) AS tourism_amt 
    from tmp 
    group by
        id_check 
) subquery
WHERE
    subquery.shoes_amt > 2 and subquery.tourism_amt >= 1
 ```
