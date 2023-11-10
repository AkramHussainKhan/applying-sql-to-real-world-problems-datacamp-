# applying-sql-to-real-world-problems-datacamp-

## Practice the essentials <br>

**In this exercise you are preparing list of your top paying active customers. The data you will need are the names of the customer sorted by the amount they paid.** <br>

```sql
SELECT first_name, 
     last_name,
       amount
FROM payment AS p
INNER JOIN customer AS c
  ON p.customer_id = c.customer_id
WHERE c.active = true
ORDER BY amount desc;
```


**Transform numeric & strings** <br>
For this exercise you are planning to run a 50% off promotion for films released prior to 2006. To prepare for this promotion you will need to return the films that qualify for this promotion, to make these titles easier to read you will convert them all to lower case. You will also need to return both the original_rate and the sale_rate.
```sql
SELECT lower(title) AS title, 
  rental_rate AS original_rate, 
  rental_rate * 0.5 AS sale_rate 
FROM film
-- Filter for films prior to 2006
where release_year <2006;
```

**Extract what you need** <br>
In this exercise you will practice preparing date/time elements by using the EXTRACT() function. <br>

•	EXTRACT the DAY from payment_date and return this column AS the payment_day.
```sql
SELECT payment_date,
  EXTRACT(day from payment_date) AS payment_day 
FROM payment;
```
•	EXTRACT the YEAR from payment_date and return this column AS the payment_year.

```sql
SELECT payment_date,
  extract(year from payment_date) AS payment_year 
FROM payment;
```
•	EXTRACT the HOUR from payment_date and return this column AS the payment_hour.

```sql
SELECT payment_date,
  extract (hour from payment_date) AS payment_hour 
FROM payment;
```

### Aggregating finances

In this exercise you would like to learn more about the differences in payments between the customers who are active and those who are not.

•	Find out the differences in their total number of payments by COUNT()ing the payment_id.

•	Find out the differences in their average payments by using AVG().

•	Find out the differences in their total payments by using SUM().

•	Ensure the aggregate functions GROUP BY whether customer payments are active.

```sql
SELECT active, 
       count(p.payment_id) AS num_transactions, 
       avg(p.amount) AS avg_amount, 
       sum(p.amount) AS total_amount
FROM payment AS p
INNER JOIN customer AS c
  ON p.customer_id = c.customer_id
GROUP BY active;
```

### Aggregating strings

You are planning to update your storefront window to demonstrate how family-friendly and multi-lingual your DVD collection is. To prepare for this you need to prepare a comma-separated list G-rated film titles by language released in 2010.

•	Return a column with a list of comma-separated film titles by using the STRING_AGG() function.

•	Limit the results to films that have a release_year of 2010 AND have a rating of 'G'.

•	Ensure that the operation is grouped by the language name.

```sql
SELECT name, 
  string_agg(title, ',') AS film_titles
FROM film AS f
INNER JOIN language AS l
  ON f.language_id = l.language_id
WHERE release_year=2010
  AND rating = 'G'
GROUP BY name;
```

**How much does this business make per month?**
```sql
SELECT EXTRACT(MONTH FROM payment_date) AS month, 
       SUM(amount) AS total_payment
FROM payment 
GROUP BY month;
```
## Find the data

**View all of the data in the information_schema.columns table by SELECTing all the columns within it.**

•	Limit your results to only the columns you need: table_name and column_name.

•	Filter the results where the table_schema is public.

```sql
SELECT table_name,column_name
FROM information_schema.columns
Where table_schema = 'public';
```


**Calculate the average_length for each category**
```sql
SELECT category, 
     avg(length) AS average_length
FROM film AS f
-- Join the tables film & category
INNER JOIN category AS c
  ON f.film_id = c.film_id
GROUP BY c.category
-- Sort the results in ascending order by length
order by average_length desc;
```

**Which films are most frequently rented?**
```sql
SELECT title, COUNT(title)
FROM film AS f
INNER JOIN inventory AS i
  ON f.film_id = i.film_id
INNER JOIN rental AS r
  ON i.inventory_id = r.inventory_id
GROUP BY title
ORDER BY count DESC;
```












































