# Домашнее задание к занятию 12.5. «Индексы» - `Гунба Леонардо`

### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```
SELECT (SUM(index_length) / SUM(data_length))*100 percentage_of_indexes
FROM INFORMATION_SCHEMA.TABLES;
```

### Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
- перечислите узкие места и оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.
    * distinct может отключать индексы, поэтому убираем его из запроса и группируем по айди покупателя, также убираем оператор OVER с partitions
    * Убираем из оператора Over f.title и запрос данных из таблицы film т.к. данные ответа запроса в селекте не предпологают заголовки фильмов.
    * можно убирать обращение  к таблице inventory и обращение  inventory_id т.к. они так же не учавствуют в фильтрации данных и на результаты выдачи не влияют

  
```sql
select concat(c.last_name, ' ', c.first_name), sum(p.amount)
from payment p, rental r, customer c
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id 
group by c.customer_id;
```
![alt tag](https://github.com/MarcinhoLeo/sbd-homework-12-05/blob/main/images/hw.png)
