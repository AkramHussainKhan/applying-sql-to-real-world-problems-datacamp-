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

**Write a query to select all records & column from the film table which have a rating of G or PG.
Save the results of the query in a new table named family_films.**

```sql
Create table family_films as
SELECT *
FROM film
WHERE rating IN ('G', 'PG');
```

## Store data

**Update the price of rentals**

You just learned that there have been some updates for the rental pricing of your films. In this exercise you will leverage the UPDATE command to modify the rental prices by increasing the rental_rate with the following logic.

All films now cost 50 cents more to rent.

R Rated films will go up by an additional 1 dollar.
```sql
UPDATE film
SET rental_rate = rental_rate+0.5;
UPDATE film
SET rental_rate = rental_rate+1
Where rating = 'R';
```

The rental company is running a promotion and needs you to lower the rental costs by 1 dollar of films who star the actors/actresses with the following last names: WILLIS, CHASE, WINSLET, GUINESS, HUDSON.

```sql
update film
set rental_rate = rental_rate-1
where film_id in 
  (SELECT film_id from actor AS a
   INNER JOIN film_actor AS f
      ON a.actor_id = f.actor_id
   WHERE last_name IN ('WILLIS', 'CHASE', 'WINSLET', 'GUINESS', 'HUDSON'));
```

### Delete selected records
You've discovered that some films are just not worth keeping your inventory, for cases where the replacement_cost is greater than 25 dollars. As such you'd like to remove them from you film table.
```sql
-- Delete films that cost most than 25 dollars
Delete from film
where replacement_cost >25;
```

### A family friendly video store
Your company has decided to become a family friendly store. As such, all R & NC-17 movies will be cleared from the inventory. You will take the steps necessary to clear these films from both the inventory and the film tables.
```sql
-- Use the list of film_id values to DELETE all R & NC-17 rated films from inventory.
delete from inventory
where film_id IN (
  SELECT film_id FROM film
  WHERE rating IN ('R', 'NC-17')
);

-- Delete records from the `film` table that are either rated as R or NC-17.
delete from film
where rating IN ('R', 'NC-17');
```
## Best practices
### Fix this query - intent
Using the four opportunities you've identified you will now clarify the intent of this query, one step at a time.
```sql
SELECT x1.customer_id, x1.rental_date, x1.return_date 
FROM rental x1
JOIN inventory x2
    ON x1.inventory_id = x2.inventory_id
JOIN film x3
    ON x2.film_id = x3.film_id
WHERE x3.length < 90;
```
And the answer is:
```sql
SELECT r.customer_id, r.rental_date, r.return_date 
FROM rental AS r
/* inventory table is used to unite the rental and film */ 
INNER JOIN inventory AS i
  ON r.inventory_id = i.inventory_id
INNER JOIN film AS f
  ON i.film_id = f.film_id
WHERE f.length < 90;
```


**Make this query easier to read  PART I**

```sql
SELECT title, rating FROM film 
WHERE rating = 'G' OR rating = 'PG' OR rating = 'R';
```
And the answer is

```sql
SELECT title, 
rating 
FROM film 
WHERE
rating IN ('G', 'PG', 'R');
```

**Make this query easier to read - Part II**

In this exercise you will work on making the query below easier to read.
```sql
select 
  category as FILMCATEGORY, 
  avg(length) as AverageLength
from film as f
inner join category as c
  on f.film_id = c.film_id
where release_year >= 2005
  and release_year <= 2010
group by category;
```

### Apply best practices to your code

In this exercise you will update the code below to adhere to the best practices you learned in this chapter.
```sql
SELECT first_name, last_name, email FROM rental AS r 
-- FROM address AS a JOIN r.address_id = a.address_id
JOIN customer AS c ON r.customer_id = c.customer_id;
```
And the answer i s
```sql
SELECT first_name, 
       last_name, 
       email 
FROM rental AS r 
INNER JOIN customer AS c 
   ON r.customer_id = c.customer_id;
```


Overall this is a nice course.
[course link](https://campus.datacamp.com/courses/applying-sql-to-real-world-problems/use-real-world-sql?ex=1)
























