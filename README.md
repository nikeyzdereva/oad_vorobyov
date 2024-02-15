## Task - 00

```sql
CREATE TABLE person_discounts(
	id BIGINT PRIMARY KEY,
	person_id BIGINT,
	pizzeria_id BIGINT,
	discount FLOAT
)
```

![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/8c66c362-b8e9-42de-88c5-9adc8b75ae25)

## Task - 01

```sql
WITH amount_of_orders AS(
	SELECT po.person_id, m.pizzeria_id, COUNT(*) FROM person_order po
	JOIN menu m ON m.id = po.menu_id 
	GROUP BY 1, 2
)

INSERT INTO person_discounts
	SELECT 
	    ROW_NUMBER() OVER (ORDER BY 1),
    	person_id,
		pizzeria_id, 
		CASE
	    	WHEN count = 1 THEN 10.5
			WHEN count = 2 THEN 22
			ELSE 30
		END
	FROM amount_of_orders 
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/4b6c858a-7ddc-4089-b5d1-42a084e897e9)


## Task - 02

```sql
SELECT 
	p.name, 
	m.pizza_name, 
	m.price, 
	m.price - (m.price / 100 * pd.discount) AS discount_price, 
	pz.name  
FROM person_order po
JOIN menu m ON m.id = po.menu_id
JOIN person_discounts pd ON pd.pizzeria_id = m.pizzeria_id AND pd.person_id = po.person_id
JOIN pizzeria pz ON pz.id = m.pizzeria_id
JOIN person p ON p.id = po.person_id
ORDER BY 1, 2

```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/5bf7b169-970f-470e-a61f-2aba8683497d)

