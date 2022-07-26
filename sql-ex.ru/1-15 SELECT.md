### 1
Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол. Вывести: model, speed и hd

```sql
SELECT model, speed, hd
FROM PC
WHERE price < 500
```

### 2

Найдите производителей принтеров. Вывести: maker

```sql
SELECT DISTINCT maker
FROM Product
WHERE type='Printer'
```

### 3

Найдите номер модели, объем памяти и размеры экранов ПК-блокнотов, цена которых превышает 1000 дол.

```sql
SELECT model, ram, screen
FROM laptop
WHERE price > 1000
```

### 4

Найдите все записи таблицы Printer для цветных принтеров.

```sql
SELECT *
FROM Printer
WHERE color = 'y'
```

### 5

Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.

```sql
SELECT model, speed, hd
FROM PC
WHERE cd IN('12x', '24x') AND price < 600
```

### 6

Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ПК-блокнотов. Вывод: производитель, скорость.

```sql
SELECT DISTINCT maker, speed
FROM Product
JOIN Laptop
	ON Laptop.model = Product.model
WHERE laptop.hd >= 10
```

### 7

Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).

```sql
SELECT 
	DISTINCT laptop.model, 
	price
FROM Product 
JOIN Laptop 
	ON Laptop.model = Product.model
WHERE maker = 'B'
union
SELECT 
	DISTINCT printer.model, 
	price
FROM product
JOIN printer 
	ON printer.model = product.model
WHERE maker = 'B'
union
SELECT
	DISTINCT pc.model, 
	price
FROM product
JOIN pc 
ON pc.model = product.model
WHERE maker = 'B'
```

### 8

Найдите производителя, выпускающего ПК, но не ПК-блокноты.

```sql
SELECT DISTINCT maker
FROM Product
WHERE type = 'PC'
except
SELECT DISTINCT maker 
FROM Product 
WHERE type = 'Laptop'
```

### 9

Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker

```sql
SELECT DISTINCT product.maker
FROM pc 
JOIN product
	ON product.model = pc.model
WHERE speed >=450
```

### 10

Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price

```sql
SELECT model, price
FROM printer
WHERE price = (SELECT max(price) FROM printer)
```

### 11

Найдите среднюю скорость ПК.

```sql
SELECT AVG(speed) FROM PC
```

### 12

Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол.

```sql
SELECT AVG(speed)
FROM Laptop
WHERE price > 1000
```

### 13

Найдите среднюю скорость ПК, выпущенных производителем A.

```sql
SELECT avg(speed)
FROM product 
JOIN pc
	ON product.model = pc.model
WHERE maker = 'A'
```
### 14

Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий.

```sql
SELECT 
	ships.class, 
	name, 
	country
FROM classes 
JOIN ships
	ON classes.class = ships.class
WHERE numguns >= 10
```

### 15

Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD

```sql
SELECT hd
FROM pc
GROUP BY hd
HAVING COUNT(hd) > 1
```
