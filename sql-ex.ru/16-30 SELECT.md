### 16
Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i).
Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.

```sql
SELECT 
	DISTINCT A.model AS model_1, 
	B.model AS model_2, 
	a.speed, 
	a.ram
FROM PC AS A, PC B
WHERE A.speed = B.speed 
	AND a.ram = b.ram 
	AND A.model > B.model
```

### 17

Найдите модели ПК-блокнотов, скорость которых меньше скорости каждого из ПК.
Вывести: type, model, speed

```sql
SELECT 
	DISTINCT type,
	product.model,
	speed
FROM product
JOIN laptop
	ON product.model = laptop.model
WHERE
	speed < ALL( SELECT speed FROM pc)
```

### 18

Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price
```sql
SELECT 
	DISTINCT maker,
	price
FROM product
JOIN printer 
	ON product.model = printer.model
WHERE price = (SELECT min(price) FROM printer WHERE color = 'y') 
			   AND color = 'y'
```

### 19

Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.
Вывести: maker, средний размер экрана.

```sql
SELECT 
	maker, 
	AVG(screen) as avg_screen
FROM product
JOIN laptop 
	ON product.model = laptop.model
GROUP BY maker
```

### 20

Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК.

```sql
SELECT
	maker, 
	COUNT(model) as COUNT_Model
FROM product
WHERE type = 'PC'
GROUP BY maker
HAVING COUNT(model) >=3
```

### 21

Найдите максимальную цену ПК, выпускаемых каждым производителем, у которого есть модели в таблице PC.
Вывести: maker, максимальная цена.

```sql
SELECT 
	maker, 
	max(price) as Max_Price
FROM product
JOIN pc 
	ON product.model = pc.model
WHERE type = 'PC'
GROUP BY maker
```

### 22

Для каждого значения скорости ПК, превышающего 600 МГц, определите среднюю цену ПК с такой же скоростью. Вывести: speed, средняя цена.

```sql
SELECT speed, avg(price) as avg_price
FROM pc
WHERE speed > 600
GROUP BY speed
```

### 23

Найдите производителей, которые производили бы как ПК
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.
Вывести: Maker

```sql
SELECT maker
FROM product
JOIN pc 
	ON product.model = pc.model
WHERE speed >= 750
GROUP BY maker
intersect
SELECT maker
FROM product
JOIN laptop 
	ON product.model = laptop.model
WHERE speed >= 750
GROUP BY maker
```

### 24

Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции.

```sql
with tmp as 
(
SELECT model, price FROM pc
union
SELECT model, price FROM laptop
union
SELECT model, price FROM printer
)

SELECT model
FROM tmp
WHERE price = (
			   SELECT max(price)
			   FROM tmp
			  )
```

### 25

Найдите производителей принтеров, которые производят ПК с наименьшим объемом RAM и с самым быстрым процессором среди всех ПК, имеющих наименьший объем RAM.
Вывести: Maker

```sql
SELECT 
	DISTINCT maker
FROM product
WHERE 
	type = 'Printer'
	AND maker in
				(SELECT maker
				 FROM product
				 JOIN pc 
				 	ON product.model = pc.model
				WHERE speed =
							 (SELECT max(speed)
							  FROM 
								  	(SELECT speed FROM pc WHERE ram = (SELECT min(ram) FROM pc)) as ze)
				AND ram = (SELECT min(ram) FROM pc))
```

### 26

Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква). Вывести: одна общая средняя цена.

```sql
SELECT avg(price)
FROM
	(
	SELECT price
	FROM product
	JOIN pc 
		ON product.model = pc.model
	WHERE maker = 'A'
	union all
	SELECT price
	FROM product
	JOIN laptop 
		ON product.model = laptop.model
	WHERE maker = 'A'
	) tmp
```

### 27

Найдите средний размер диска ПК каждого из тех производителей, которые выпускают и принтеры. Вывести: maker, средний размер HD.

```sql
SELECT 
	maker,
	avg(hd)
FROM product 
JOIN pc 
	ON product.model = pc.model
WHERE maker in (SELECT maker FROM product WHERE type = 'Printer')
GROUP BY maker
```

### 28

Используя таблицу Product, определить количество производителей, выпускающих по одной модели.

```sql
SELECT
	COUNT(maker)
FROM
	(
	SELECT maker
	FROM product
	GROUP BY maker
	HAVING COUNT(model) = 1
	) tmp
```
### 29
В предположении, что приход и расход денег на каждом пункте приема фиксируется не чаще одного раза в день [т.е. первичный ключ (пункт, дата)], написать запрос с выходными данными (пункт, дата, приход, расход). Использовать таблицы Income_o и Outcome_o.

```sql
SELECT
    l.*,
    r.out
FROM Income_o l
LEFT JOIN Outcome_o r
    ON l.point = r.point
    AND l.date = r.date
UNION
SELECT
    r.point,
    r.date,
    l.inc,
    r.out
FROM Income_o l
RIGHT JOIN Outcome_o r
    ON l.point = r.point
    AND l.date = r.date
ORDER BY DATE
```

### 30
В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз (первичным ключом в таблицах является столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату выполнения операций будет соответствовать одна строка.
Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc). Отсутствующие значения считать неопределенными (NULL).

```sql
SELECT
    point,
    date,
    sum(out) as out,
    sum(inc) as inc
FROM
(
	SELECT
	    point,
	    date,
	    NULL as out,
	    SUM(inc) as inc
	FROM Income
	GROUP BY 
	    point, date
	UNION
	SELECT
	    point,
	    date,
	    SUM(out) as out,
	    NULL
	FROM Outcome
	GROUP BY 
	    point, date
) tmp
GROUP BY 
    point, date
```
