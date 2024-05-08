Task-00
```sql
SELECT person_id, COUNT(*) AS count_of_visits
FROM person_order
GROUP BY person_id
ORDER BY count_of_visits DESC, person_id ASC;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/5363a34c-f3aa-44bf-b52d-a69aad6531b0)

Task-01
```sql
SELECT p.name, COUNT(pv.id) as count_of_visits
FROM person_visits pv
JOIN person p ON p.id = pv.person_id
GROUP BY p.name
ORDER BY count_of_visits DESC, p.name
LIMIT 4;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/d6903b4f-78e8-4b22-bca9-8e325fd57071)

Task-03
```sql
WITH visits AS (
 SELECT piz.name, COUNT(pv.id) as visit_count
 FROM person_visits pv
 JOIN pizzeria piz ON piz.id = pv.pizzeria_id
 GROUP BY piz.name
), 
orders AS (
 SELECT piz.name, COUNT(po.id) as order_count
 FROM person_order po
 JOIN menu m ON m.id = po.menu_id
 JOIN pizzeria piz ON piz.id = m.pizzeria_id
 GROUP BY piz.name
),
combined AS (
 SELECT visits.name, (visits.visit_count + COALESCE(orders.order_count, 0)) as total_count
 FROM visits
 LEFT JOIN orders ON visits.name = orders.name
 UNION ALL
 SELECT orders.name, (COALESCE(visits.visit_count, 0) + orders.order_count) as total_count
 FROM orders
 LEFT JOIN visits ON orders.name = visits.name
)

SELECT name, SUM(total_count) as total_count
FROM combined
GROUP BY name
ORDER BY total_count DESC, name ASC;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/547bf327-d23d-476e-89d7-761b8d54692c)

Task-04
```sql
WITH visit_counts AS (
    SELECT p.name AS name, COUNT(*) AS count_of_visits
    FROM person_visits v
    JOIN person p ON v.person_id = p.id
    GROUP BY p.name )
SELECT name, count_of_visits
FROM visit_counts
WHERE count_of_visits > 2;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/b23926b5-2cbf-4c31-a432-5225c7516b11)
если брать больше 3 заказов
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/038fe790-0fbf-4944-834f-46699a21e4e3)
если больше 2

Task-05
```sql
SELECT DISTINCT p.name AS name
FROM person_order o
JOIN person p ON o.person_id = p.id
ORDER BY name;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/4875068a-34c3-4035-9fa5-070e70ecd37c)

Task-06
```sql
SELECT 
    piz.name, 
    COUNT(po.id) as count_of_orders, 
    ROUND(AVG(m.price), 2) as average_price, 
    MAX(m.price) as max_price, 
    MIN(m.price) as min_price 
FROM person_order po
JOIN menu m ON m.id = po.menu_id
JOIN pizzeria piz ON piz.id = m.pizzeria_id
GROUP BY piz.name
ORDER BY piz.name;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/c51e5d92-ca56-4e10-90b9-42eada708f5a)

Task-07
```sql
SELECT ROUND(AVG(rating)::numeric, 4) AS global_rating
FROM pizzeria;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/79a98f88-6086-4679-bd8b-ddf769352d62)

Task-08
```sql
SELECT p.address, piz.name AS name, COUNT(po.id) AS count_of_orders
FROM person_order po
JOIN menu m ON m.id = po.menu_id
JOIN pizzeria piz ON piz.id = m.pizzeria_id
JOIN person p ON p.id = po.person_id
GROUP BY p.address, piz.name
ORDER BY p.address, piz.name;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/4d942861-565c-4cfe-8887-789905440d88)
Task-09
```sql
SELECT 
    p.address,
    MAX(p.age) - (MIN(p.age) / MAX(p.age)) AS formula,
    ROUND(AVG(p.age)::numeric, 2) as average,
    CASE
        WHEN MAX(p.age) - (MIN(p.age) / MAX(p.age)) > AVG(p.age) THEN 'true'
        ELSE 'false'
    END AS comparison
FROM person p
GROUP BY p.address
ORDER BY p.address;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/3c4f9ea7-496e-444a-b45c-e83372b2152e)



