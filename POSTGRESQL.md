# POSTGRESQL

## RECURSOS
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
- [Curso Sql Básico con Postgres - May Code](https://www.youtube.com/watch?v=OYwF2W2E2fg)

### SELECT
```sql
select * from actor;

select distinct first_name from actor;
select distinct last_name from actor;
select distinct first_name, last_name from actor;

select distinct on (last_name) last_name , first_name from actor;
```

### ALIAS
```sql
select first_name primer_nombre, last_name AS primer_apellido from actor;
```

### ORDER BY 
default asc
```sql
select * from actor
ORDER BY first_name ASC, actor_id ASC

select first_name, last_name
from actor
ORDER BY RIGHT(last_name,1)
```

## WHERE, AND OR IS NULL = != <> < <= > >= BETWEEN NOT LIKE IN 
```sql
select * from rental
WHERE (customer_id > 305 AND customer_id <= 310) OR customer_id = 459;

select last_name, first_name
from customer
where first_name = 'Jamief'
and last_name = 'Rice'

select first_name, last_name
from customer
WHERE first_name NOT IN ('Ann','Anne','Annie','Mary')

select customer_id, rental_id, return_date
from rental
where customer_id in (1,2,3,4,5)
ORDER BY return_date DESC

select customer_id, rental_id, return_date
from rental
where customer_id in (
	SELECT customer_id
	from customer
	WHERE first_name = 'Jamie'
)
ORDER BY return_date DESC

select customer_id, payment_id, amount
from payment
where amount between 7 and 9

select customer_id, payment_id, amount
from payment
where amount not between 7 and 9

select customer_id, payment_id, amount, payment_date
from payment
where payment_date between '2007-02-07' and '2007-02-15'
```

### LIKE
%cualquier caracter,muchas veces _cualquier caracter una vez
```sql
select first_name , last_name
from customer
where first_name LIKE 'A%'


select first_name , last_name
from customer
where first_name LIKE '_her%'
```

### ILIKE 
No importa la mayuscula o minuscula
```sql
SELECT * from address
where address2 IS  NULL

SELECT address2 from address
where address2 IS NOT NULL
```
### INSERT 
Agregar registro a la tabla
```sql
INSERT INTO country (country,last_update)
VALUES ('Guatemala', NOW())
RETURNING *;


INSERT INTO country 
VALUES (111,'TEst', NOW());

select * from country;

INSERT INTO country(country)
VALUES ('Guatemala1'), ('Guatemala2')
RETURNING country_id

INSERT INTO country(country)
SELECT country || ' copy'
from country where country_id > 100;
```

### UPDATE
```sql
UPDATE address
SET postal_code = NULL
WHERE address_id = 5;
```

### DELETE
```sql
DELETE FROM address
WHERE address_id = 5;
```

## DDL
```sql
CREATE ALTER DROP
CREATE DATABASE dvdrental;

CREATE SCHEMA TEST;

CREATE TABLE TEST.customer (
customer_id integer DEFAULT nextval('public.customer_customer_id_seq') NOT NULL,
	store_id smallint NOT NULL,
	first_name character varying(45) NOT NULL,
	activebool boolean DEFAULT true NOT NULL,
);
```

### NOT NULL UNIQUE PRIMARY KEY FOREIGN KEY CHECK DEFAULT CREATE INDEX
```sql
create table invoices(
id SERIAL PRIMARY KEY,
product_id INT NOT NULL UNIQUE,
	qty numeric NOT NULL CHECK(qty > 0),
	net_price numeric CHECK(net_price > 0),
);

ALTER TABLE invoices ADD PRIMARY KEY(id);
ALTER TABLE invoices ADD CONSTRAINT invoices_pk PRIMARY KEY(id);

ALTER TABLE inovices
ALTER COLUMN net_price
SET NOT_NULL;

CREATE TABLE roles(
	role_id serial PRIMARY KEY,
	role_name VARCHAR(255) UNIQUE NOT NULL
);


CREATE TABLE account_roles(
	user_id INT NOT NULL,
	role_id INT NOT NULL.
	grant_date TIMESTAMP,
	PRIMARY KEY(user_id, role_id),
	FOREIGN KEY(role_id)
		REFERENCES roles(role_id),
	FOREIGN KEY(user_id)
		REFERENCES accounts(user_id),
);

ALTER TABLE users add column birthdate DATE;
ALTER TABLE users rename to dvdrental_users;

DROP DATABASE NAME_DB;
DROP TABLE NAME_TABLE;
DROP SCHEMA NAME_SCHEMA;
```

### Tablas temporales
```sql
CREATE TEMP TABLE nombre_tabla_temporal(
 id serial primary key,
	username varchar(50),
	password varchar(50),
	email varchar(50)
);

CREATE TEMP TABLE a 
as 
SELECT first_name 
FROM actor;
```

## JOINS
SELECT campo(s)
FROM tabla_A a
[INNER | LEFT | RIGHT | FULL ] JOIN Tabla_B B ON A.Key = B.Key

### INNER JOIN 
interseccion
```sql
SELECT film_id, title, language_id
FROM film;

SELECT * from language;

SELECT film_id, title, description, release_year,
B.language_id, B.name
FROM film A
INNER JOIN language B ON A.language_id = B.language_id;

SELECT film_id, title, description, release_year,
B.language_id, B.name
FROM film A
JOIN language B using(language_id);

SELECT inventory_id,film_id, A.store_id, B.staff_id,
B.first_name, B.last_name
INNER JOIN staff B ON A.store_id = B.store_id
WHERE A.film_id  <= 10
ORDER BY A.film_id;
```

### LEFT JOIN
```sql
SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM address A
LEFT JOIN customer B ON A.address_id = B.address_id;


SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM address A
LEFT JOIN customer B ON A.address_id = B.address_id;
WHERE B.customer_id IS NULL;
```


### RIGHT JOIN
```sql
SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM customer A
RIGHT JOIN address B ON A.address_id = B.address_id;

SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM customer A
RIGHT JOIN address B  ON A.address_id = B.address_id;
WHERE A.customer_id IS NULL;
```

### FULL JOIN
```sql
SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM customer A
FULL OUTER JOIN address B ON A.address_id = B.address_id;

SELECT A.address_id, B.customer_id,B.first_name, B.last_name
FROM customer A
FULL OUTER JOIN address B ON A.address_id = B.address_id
WHERE A.address_id is NULL
OR B.address_id IS NULL;
```

### SELF JOIN
```sql
SELECT A.title, B.title, A,length
FROM film A
LEFT JOIN film B ON A.film_id <> B.film_id
 AND A.lenght = B.length;
 ```
 
### CROSS JOIN
```sql
SELECT * 
FROM film
CROSS JOIN category;

SELECT *
FROM film, category;
```

### NATURAL [INNER,LEFT,RIGHT] JOIN
```sql
SELECT * 
FROM film
NATURAL INNER JOIN category;

SELECT , C.name
FROM film A
INNER JOIN film_category B ON B.film_id = A.film_id
INNeR JOIN category C ON B.category_id = C.category_id;
```

### FUNCIONES DE AGREGACION
-  AVG
-  COUNT
- COUNT(DISTINCT)
- MAX 
- MIN 
- SUM 
- STRING_AGG
- ARRAY_AGG

```sql
select first_name, COUNT(first_name)
FROM customer
group by first_name
having count(first_name) > 1;
```

### UNION , UNION ALL , INTERSECT 
Para Queries

```sql
SELECT campos
FROM nombre_tabla_A
UNION | UNION ALL | INTERSECT

SELECT campos
FROM nombre_tabla_B
```

### Subconsultas
Un solo valor o varias filas de valores

```sql
SELECT * FROM actor
WHERE actor_id IN (
	SELECT actor_id
	FROM film_actor
	GROUP BY actor_id
	having COUNT(film_id) >= 40
);

SELECT * from film
where length  = (
	SELECT MAX(length)
	from film
);

SELECT film_id, title, description, length, (SELECT MAX(length)
	from film) max_length from film;
	
SELECT * 
from film F
where length = (
	SELECT MAX(length)
	from film
	where rental_duration = F.rental_duration
);

SELECT A.actor_id, A.first_name, A.last_name, F.total_films
FROM actor A
INNER JOIN (
	SELECT actor_id , COUNT(film_id) total_films
	FROM film_actor
	GROUP BY actor_id
) F ON A.actor_id = F.actor_id
WHERE total_films >= 40;
```

### COMMON TABLE EXPRESSIONS
```sql
 WITH alias_del_CTE AS
 (
 	SELECT campo(s)
 	FROM tabla(s)
 )
 SELECT | INSERT | UPDATE | DELETE
```

### VISTAS
```sql
CREATE VIEW NOMBRE
AS
SELECT * 
FROM actor
WHERE last_name LIKE 'A%';
```

### Usuarios y Roles Data Control Language DCL
- GRANT agregar permiso
- REVOKE revocar permiso
- GRANT | REVOKE + Privilegios + ON + nombre_objecto + TO nombre_usuario | role
-  SELECT, UPDATE, DELETE, INSERT , ALL PRIVILEGES
- CREATE USER WEbsite WITH PASSWORD "contra1";

- REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM WEbsite;
- CREATE ROLE role1;

### Tipos
 
- Boolean true false
- Cadenas de texto varchar(10), char(10), text
- Numericos NUMERIC(precision, scale), NUMERIC(precision), NUMERIC
- SERIAL, SMALLSERIAL, BIGSERIAL , entero secuencia automatica
- DATE,TIMESTAMP, TIME, UUID, JSON, HSTORE , ARRAY , User-defined

```sql
TO_CHAR(DATE, fomato ='dd/mm/yyyy')
EXTRAT(DAY|YEAR|MONTH FROM DATE) AS DAY

TIMESTAMP,TIMESTAMPZ
timezone('America/Lima', timestampz)
INTERVAL '1year 3 hours 20 minutes'
```

### BACKUP
 restauracion
```sql
CREATE DATABASE dvdrental_dev
WITH TEMPLATE dvdrental;

SELECT pid,usename, client_addr
FROM pg_stat_activity
WHERE datname = 'dvdrental'

SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE datname = 'dvdrental'
```

backup
```cmd
pg_dump -U postgres dvdrental > "Ubicacion.sql"
```
restaurar a remoto 
```cmd
 pg_dump -C -h localhost -U postgres dvdrental | psql -h remote(url servidor) -U postgres dvdrental
```