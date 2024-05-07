Task-00
```sql
CREATE TABLE person_discounts (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  person_id BIGINT NOT NULL,
  pizzeria_id BIGINT NOT NULL,
  discount FLOAT DEFAULT 0.0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  CONSTRAINT fk_person_discounts_person_id FOREIGN KEY (person_id) REFERENCES persons(id),
  CONSTRAINT fk_person_discounts_pizzeria_id FOREIGN KEY (pizzeria_id) REFERENCES pizzerias(id)
);

```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/2e86b322-524b-4fb2-b94c-df921908c361)

Task-01
```sql
WITH amount_of_orders AS (
  SELECT po.person_id, m.pizzeria_id, COUNT(*) AS order_count
  FROM person_order po
  JOIN menu m ON m.id = po.menu_id
  GROUP BY po.person_id, m.pizzeria_id
  ORDER BY po.person_id, m.pizzeria_id
)
INSERT INTO person_discounts (id, person_id, pizzeria_id, discount)
SELECT
  ROW_NUMBER() OVER (ORDER BY ao.person_id, ao.pizzeria_id) AS id,
  ao.person_id,
  ao.pizzeria_id,
  CASE
    WHEN ao.order_count = 1 THEN 10.5
    WHEN ao.order_count = 2 THEN 22
    ELSE 30
  END AS discount
FROM amount_of_orders ao;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/fc8c0de0-ed73-4c4f-a9f8-6d2c267786ec)

Task-02
```sql
SELECT
  p.name AS person_name,
  m.pizza_name,
  m.price AS original_price,
  (m.price - (m.price * pd.discount / 100)) AS discounted_price,
  piz.name AS pizzeria_name
FROM menu m
JOIN person_order po ON po.menu_id = m.id
JOIN person p ON p.id = po.person_id
JOIN person_discounts pd ON pd.person_id = p.id AND pd.pizzeria_id = m.pizzeria_id
JOIN pizzeria piz ON piz.id = m.pizzeria_id
ORDER BY person_name, pizza_name, discounted_price DESC, pizzeria_name;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/6b944361-43b7-4476-908c-2d57064ff0f2)

Task-03
```sql
CREATE UNIQUE INDEX idx_person_discounts_unique 
ON person_discounts (person_id, pizzeria_id);
```

```sql
EXPLAIN ANALYZE 
SELECT * 
FROM person_discounts 
WHERE person_id = 123 AND pizzeria_id = 456;
немного не пон задание
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/120989f8-ad1c-4e12-8306-8d8b586d9174)

Task-04
```sql
ALTER TABLE person_discounts
ADD CONSTRAINT ch_nn_person_id CHECK (person_id IS NOT NULL);

ALTER TABLE person_discounts
ADD CONSTRAINT ch_nn_pizzeria_id CHECK (pizzeria_id IS NOT NULL);

ALTER TABLE person_discounts
ADD CONSTRAINT ch_nn_discount CHECK (discount IS NOT NULL);

ALTER TABLE person_discounts
ALTER COLUMN discount SET DEFAULT 0;

ALTER TABLE person_discounts
ADD CONSTRAINT ch_range_discount CHECK (discount >= 0 AND discount <= 100);
```
Task-05
```sql
COMMENT ON TABLE person_discounts IS 'Эта таблица хранит информацию о скидках, применяемых к клиентам в различных пиццериях.';

COMMENT ON COLUMN person_discounts.person_id IS 'Идентификатор лица, получающего скидку.';
COMMENT ON COLUMN person_discounts.pizzeria_id IS 'Идентификатор пиццерии, в которой применяется скидка.';
COMMENT ON COLUMN person_discounts.discount IS 'Процент скидки, применяемый к клиенту.';
```
Task-06
```sql
CREATE SEQUENCE seq_person_discounts;
```
![image](https://github.com/nikeyzdereva/oad_vorobyov/assets/112609367/ba5a5b7c-e57f-4b99-ae54-83614b09f140)
