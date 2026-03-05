# Sql-task
Задачи по Sql

1 какую сумму в среднем в месяц тратит:

пользователи в возрастном диапазоне от 18 до 25 лет включительно
пользователи в возрастном диапазоне от 26 до 35 лет включительно
2 в каком месяце года выручка от пользователей в возрастном диапазоне 35+ самая большая

3 какой товар обеспечивает наибольший вклад в выручку за последний год

4 топ-3 товаров по выручке и их доля в общей выручке за любой год

## 1
```
SELECT AVG(a.summa)
   FROM (
   SELECT SUM(pr.cost) AS summa, strftime('%Y-%m', p.date) AS date
   FROM Products pr
   JOIN Purchases p 
   ON p.productId = pr.productidid
   WHERE p.customerId IN (SELECT c.customerId FROM Customers c WHERE c.age BETWEEN 20 and 30)
   GROUP BY strftime('%Y-%m', p.date)
         ) AS a 
  
SELECT AVG(a.summa)
  FROM (
  SELECT SUM(pr.cost) AS summa, strftime('%Y-%m', p.date) AS date
  FROM Products pr
  JOIN Purchases p 
  ON p.productId = pr.productidid
  WHERE p.customerId IN (SELECT c.customerId FROM Customers c WHERE c.age BETWEEN 30 and 40)
  GROUP BY strftime('%Y-%m', p.date)
  ) AS a 
```
## 2
```
SELECT a.product
   FROM (
   SELECT SUM(pr.cost) as summa, pr.productIdid as product
   FROM Products pr
     JOIN Purchases p
     ON   pr.productidId = p.productId
     WHERE strftime('%Y-%m-%d', p.date) 
     BETWEEN strftime('%Y-%m-%d', 'now', '-1 year') 
     AND strftime('%Y-%m-%d', 'now') 
     GROUP BY pr.productidId
        ) AS a
      ORDER BY a.summa DESC
      LIMIT 1;
   ```         

## 3
```
SELECT top_items.itemId, INTEGER(top_items.amount*100 /  (SELECT SUM(i.price)
                                                                            FROM Items i
                                                                            JOIN Purchases p
                                                                            ON i.itemId = p.itemId))
                    FROM (SELECT i.itemId, SUM(i.price) AS amount
                            FROM  Items i
                            JOIN  Purchases p
                              ON  i.itemId = p.itemId
                            WHERE strftime('%Y', p.date) = '{year}'
                            GROUP BY i.itemId
                            ORDER BY amount DESC
                            LIMIT 5) AS top_items
           
```
## 4
```
SELECT 
    top_products.productidId, 
    CAST(top_products.amount * 100 / (
        SELECT SUM(pr.cost)
        FROM Products pr
        JOIN Purchases p ON pr.productidid = p.productId
    ) AS INTEGER) AS percentage
FROM (
    SELECT pr.productidId, SUM(pr.cost) AS amount
    FROM Products pr
    JOIN Purchases p ON pr.productIdId = p.productId
    WHERE strftime('%Y', p.date) = '2026'
    GROUP BY pr.productIdId
    ORDER BY amount DESC
    LIMIT 5
) AS top_products; 
```
