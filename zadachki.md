1.
SELECT u.name AS user_name, r.date, SUM(s.price) AS total_sum, s.title AS services_title
FROM users u
JOIN records r ON u.ID = r.user_ID
JOIN services_records sr ON r.ID = sr.records
JOIN services s ON sr.service_id = s.ID
GROUP BY u.name, r.date;



2.
INSERT INTO records (user_ID, date)
VALUES (value_user_ID, value_date);
SET @last_record_id = LAST_INSERT_ID();
INSERT INTO services_records (service_id, records)
VALUES (value_service_id, @last_record_id);
