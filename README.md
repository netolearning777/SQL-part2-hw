# Домашнее задание к занятию "SQL. Часть 2" - Гусев Алексей

### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)
---

### Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:
* фамилия и имя сотрудника из этого магазина;
* город нахождения магазина;
* количество пользователей, закреплённых в этом магазине.

```sql
SELECT 
    st.first_name AS "Имя сотрудника",
    st.last_name AS "Фамилия сотрудника",
    ci.city AS "Город",
    COUNT(cu.customer_id) AS "Количество покупателей"
FROM store s
JOIN staff st ON s.manager_staff_id = st.staff_id
JOIN address a ON s.address_id = a.address_id
JOIN city ci ON a.city_id = ci.city_id
JOIN customer cu ON s.store_id = cu.store_id
GROUP BY s.store_id, st.first_name, st.last_name, ci.city
HAVING COUNT(cu.customer_id) > 300;
```
1. Связывание таблиц (JOIN): соединяет магазин с его менеджером, адресом и городом, а также с клиентами, которые привязаны к магазину.
2. Группировка (GROUP BY): данные группирует по магазину, чтобы посчитать клиентов для каждого филиала отдельно.
3. Агрегация (COUNT): считает количество покупателей, закрепленных за магазином.
4. Фильтрация (HAVING): отсекает магазины, в которых покупателей меньше или равно 300 (в отличие от WHERE, HAVING фильтрует уже после подсчета функции COUNT).

### Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.
```sql
SELECT COUNT(*) AS count_movies
FROM movies
WHERE duration > (SELECT AVG(duration) FROM movies);
```
1. (SELECT AVG(duration) FROM movies) — подзапрос, вычисляет среднюю продолжительность всех фильмов в таблице.
2. WHERE duration > — фильтрует строки, оставляя фильмы, чья продолжительность превышает среднее значение.
3. SELECT COUNT(*) — подсчитывает итоговое количество фильмов.

### Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.
```sql
SELECT 
    DATE_FORMAT(payment_date, '%Y-%m') AS month,
    SUM(amount) AS total_payments,
    COUNT(DISTINCT rental_id) AS total_rentals
FROM 
    payment
GROUP BY 
    month
ORDER BY 
    total_payments DESC
LIMIT 1;
```

