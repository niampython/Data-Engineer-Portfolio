

# **DVD Rental’s Analysis Project**

### **Difficulty Level: Advanced**


## **Project Overview**

I have worked on analyzing a dataset of over 16,000 DVD rental records from a DVD Rentals dataset. This project involves extensive querying of customer transactions, actors and films performance, and sales trends by stores, customers, and countries
 using PostgreSQL. Through this project, I have tackled various SQL problems, including revenue analysis, customer segmentation, and inventory management.
The project also focuses on solving real-world business problems using structured queries and functions such as window functions, date functions, aggregates, grouping, case statements, string functions, joining tables and much more.
An ERD diagram is included to visually represent the database schema and relationships between tables.



## **Database Setup & Design**

### **Schema Structure**
```sql
BEGIN;


CREATE TABLE IF NOT EXISTS public.actor
(
    actor_id integer NOT NULL DEFAULT nextval('actor_actor_id_seq'::regclass),
    first_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    last_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT actor_pkey PRIMARY KEY (actor_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.address
(
    address_id integer NOT NULL DEFAULT nextval('address_address_id_seq'::regclass),
    address character varying(50) COLLATE pg_catalog."default" NOT NULL,
    address2 character varying(50) COLLATE pg_catalog."default",
    district character varying(20) COLLATE pg_catalog."default" NOT NULL,
    city_id smallint NOT NULL,
    postal_code character varying(10) COLLATE pg_catalog."default",
    phone character varying(20) COLLATE pg_catalog."default" NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT address_pkey PRIMARY KEY (address_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.category
(
    category_id integer NOT NULL DEFAULT nextval('category_category_id_seq'::regclass),
    name character varying(25) COLLATE pg_catalog."default" NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT category_pkey PRIMARY KEY (category_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.city
(
    city_id integer NOT NULL DEFAULT nextval('city_city_id_seq'::regclass),
    city character varying(50) COLLATE pg_catalog."default" NOT NULL,
    country_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT city_pkey PRIMARY KEY (city_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.country
(
    country_id integer NOT NULL DEFAULT nextval('country_country_id_seq'::regclass),
    country character varying(50) COLLATE pg_catalog."default" NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT country_pkey PRIMARY KEY (country_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.customer
(
    customer_id integer NOT NULL DEFAULT nextval('customer_customer_id_seq'::regclass),
    store_id smallint NOT NULL,
    first_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    last_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    email character varying(50) COLLATE pg_catalog."default",
    address_id smallint NOT NULL,
    activebool boolean NOT NULL DEFAULT true,
    create_date date NOT NULL DEFAULT ('now'::text)::date,
    last_update timestamp without time zone DEFAULT now(),
    active integer,
    CONSTRAINT customer_pkey PRIMARY KEY (customer_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.film
(
    film_id integer NOT NULL DEFAULT nextval('film_film_id_seq'::regclass),
    title character varying(255) COLLATE pg_catalog."default" NOT NULL,
    description text COLLATE pg_catalog."default",
    release_year year,
    language_id smallint NOT NULL,
    original_language_id smallint,
    rental_duration smallint NOT NULL DEFAULT 3,
    rental_rate numeric(4, 2) NOT NULL DEFAULT 4.99,
    length smallint,
    replacement_cost numeric(5, 2) NOT NULL DEFAULT 19.99,
    rating mpaa_rating DEFAULT 'G'::mpaa_rating,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    special_features text[] COLLATE pg_catalog."default",
    fulltext tsvector NOT NULL,
    CONSTRAINT film_pkey PRIMARY KEY (film_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.film_actor
(
    actor_id smallint NOT NULL,
    film_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT film_actor_pkey PRIMARY KEY (actor_id, film_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.film_category
(
    film_id smallint NOT NULL,
    category_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT film_category_pkey PRIMARY KEY (film_id, category_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.inventory
(
    inventory_id integer NOT NULL DEFAULT nextval('inventory_inventory_id_seq'::regclass),
    film_id smallint NOT NULL,
    store_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT inventory_pkey PRIMARY KEY (inventory_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.language
(
    language_id integer NOT NULL DEFAULT nextval('language_language_id_seq'::regclass),
    name character(20) COLLATE pg_catalog."default" NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT language_pkey PRIMARY KEY (language_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.nyweather
(
    station character varying(255) COLLATE pg_catalog."default",
    station_name character varying(255) COLLATE pg_catalog."default",
    date date,
    prcp numeric(5, 4)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL,
    CONSTRAINT payment_pkey PRIMARY KEY (payment_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_01
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_02
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_03
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_04
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_05
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.payment_p2007_06
(
    payment_id integer NOT NULL DEFAULT nextval('payment_payment_id_seq'::regclass),
    customer_id smallint NOT NULL,
    staff_id smallint NOT NULL,
    rental_id integer NOT NULL,
    amount numeric(5, 2) NOT NULL,
    payment_date timestamp without time zone NOT NULL
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.rental
(
    rental_id integer NOT NULL DEFAULT nextval('rental_rental_id_seq'::regclass),
    rental_date timestamp without time zone NOT NULL,
    inventory_id integer NOT NULL,
    customer_id smallint NOT NULL,
    return_date timestamp without time zone,
    staff_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT rental_pkey PRIMARY KEY (rental_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.staff
(
    staff_id integer NOT NULL DEFAULT nextval('staff_staff_id_seq'::regclass),
    first_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    last_name character varying(45) COLLATE pg_catalog."default" NOT NULL,
    address_id smallint NOT NULL,
    email character varying(50) COLLATE pg_catalog."default",
    store_id smallint NOT NULL,
    active boolean NOT NULL DEFAULT true,
    username character varying(16) COLLATE pg_catalog."default" NOT NULL,
    password character varying(40) COLLATE pg_catalog."default",
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    picture bytea,
    CONSTRAINT staff_pkey PRIMARY KEY (staff_id)
)
WITH (
    OIDS = FALSE
);

CREATE TABLE IF NOT EXISTS public.store
(
    store_id integer NOT NULL DEFAULT nextval('store_store_id_seq'::regclass),
    manager_staff_id smallint NOT NULL,
    address_id smallint NOT NULL,
    last_update timestamp without time zone NOT NULL DEFAULT now(),
    CONSTRAINT store_pkey PRIMARY KEY (store_id)
)
WITH (
    OIDS = FALSE
);

ALTER TABLE IF EXISTS public.address
    ADD CONSTRAINT address_city_id_fkey FOREIGN KEY (city_id)
    REFERENCES public.city (city_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_city_id
    ON public.address(city_id);


ALTER TABLE IF EXISTS public.city
    ADD CONSTRAINT city_country_id_fkey FOREIGN KEY (country_id)
    REFERENCES public.country (country_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_country_id
    ON public.city(country_id);


ALTER TABLE IF EXISTS public.customer
    ADD CONSTRAINT customer_address_id_fkey FOREIGN KEY (address_id)
    REFERENCES public.address (address_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_address_id
    ON public.customer(address_id);


ALTER TABLE IF EXISTS public.customer
    ADD CONSTRAINT customer_store_id_fkey FOREIGN KEY (store_id)
    REFERENCES public.store (store_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_store_id
    ON public.customer(store_id);


ALTER TABLE IF EXISTS public.film
    ADD CONSTRAINT film_language_id_fkey FOREIGN KEY (language_id)
    REFERENCES public.language (language_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_language_id
    ON public.film(language_id);


ALTER TABLE IF EXISTS public.film
    ADD CONSTRAINT film_original_language_id_fkey FOREIGN KEY (original_language_id)
    REFERENCES public.language (language_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_original_language_id
    ON public.film(original_language_id);


ALTER TABLE IF EXISTS public.film_actor
    ADD CONSTRAINT film_actor_actor_id_fkey FOREIGN KEY (actor_id)
    REFERENCES public.actor (actor_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.film_actor
    ADD CONSTRAINT film_actor_film_id_fkey FOREIGN KEY (film_id)
    REFERENCES public.film (film_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_film_id
    ON public.film_actor(film_id);


ALTER TABLE IF EXISTS public.film_category
    ADD CONSTRAINT film_category_category_id_fkey FOREIGN KEY (category_id)
    REFERENCES public.category (category_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.film_category
    ADD CONSTRAINT film_category_film_id_fkey FOREIGN KEY (film_id)
    REFERENCES public.film (film_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.inventory
    ADD CONSTRAINT inventory_film_id_fkey FOREIGN KEY (film_id)
    REFERENCES public.film (film_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.inventory
    ADD CONSTRAINT inventory_store_id_fkey FOREIGN KEY (store_id)
    REFERENCES public.store (store_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.payment
    ADD CONSTRAINT payment_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_customer_id
    ON public.payment(customer_id);


ALTER TABLE IF EXISTS public.payment
    ADD CONSTRAINT payment_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE SET NULL;


ALTER TABLE IF EXISTS public.payment
    ADD CONSTRAINT payment_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_staff_id
    ON public.payment(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_01
    ADD CONSTRAINT payment_p2007_01_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_01_customer_id
    ON public.payment_p2007_01(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_01
    ADD CONSTRAINT payment_p2007_01_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_01
    ADD CONSTRAINT payment_p2007_01_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_01_staff_id
    ON public.payment_p2007_01(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_02
    ADD CONSTRAINT payment_p2007_02_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_02_customer_id
    ON public.payment_p2007_02(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_02
    ADD CONSTRAINT payment_p2007_02_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_02
    ADD CONSTRAINT payment_p2007_02_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_02_staff_id
    ON public.payment_p2007_02(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_03
    ADD CONSTRAINT payment_p2007_03_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_03_customer_id
    ON public.payment_p2007_03(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_03
    ADD CONSTRAINT payment_p2007_03_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_03
    ADD CONSTRAINT payment_p2007_03_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_03_staff_id
    ON public.payment_p2007_03(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_04
    ADD CONSTRAINT payment_p2007_04_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_04_customer_id
    ON public.payment_p2007_04(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_04
    ADD CONSTRAINT payment_p2007_04_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_04
    ADD CONSTRAINT payment_p2007_04_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_04_staff_id
    ON public.payment_p2007_04(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_05
    ADD CONSTRAINT payment_p2007_05_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_05_customer_id
    ON public.payment_p2007_05(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_05
    ADD CONSTRAINT payment_p2007_05_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_05
    ADD CONSTRAINT payment_p2007_05_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_05_staff_id
    ON public.payment_p2007_05(staff_id);


ALTER TABLE IF EXISTS public.payment_p2007_06
    ADD CONSTRAINT payment_p2007_06_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_06_customer_id
    ON public.payment_p2007_06(customer_id);


ALTER TABLE IF EXISTS public.payment_p2007_06
    ADD CONSTRAINT payment_p2007_06_rental_id_fkey FOREIGN KEY (rental_id)
    REFERENCES public.rental (rental_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.payment_p2007_06
    ADD CONSTRAINT payment_p2007_06_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;
CREATE INDEX IF NOT EXISTS idx_fk_payment_p2007_06_staff_id
    ON public.payment_p2007_06(staff_id);


ALTER TABLE IF EXISTS public.rental
    ADD CONSTRAINT rental_customer_id_fkey FOREIGN KEY (customer_id)
    REFERENCES public.customer (customer_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.rental
    ADD CONSTRAINT rental_inventory_id_fkey FOREIGN KEY (inventory_id)
    REFERENCES public.inventory (inventory_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_fk_inventory_id
    ON public.rental(inventory_id);


ALTER TABLE IF EXISTS public.rental
    ADD CONSTRAINT rental_staff_id_fkey FOREIGN KEY (staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.staff
    ADD CONSTRAINT staff_address_id_fkey FOREIGN KEY (address_id)
    REFERENCES public.address (address_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.staff
    ADD CONSTRAINT staff_store_id_fkey FOREIGN KEY (store_id)
    REFERENCES public.store (store_id) MATCH SIMPLE
    ON UPDATE NO ACTION
    ON DELETE NO ACTION;


ALTER TABLE IF EXISTS public.store
    ADD CONSTRAINT store_address_id_fkey FOREIGN KEY (address_id)
    REFERENCES public.address (address_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;


ALTER TABLE IF EXISTS public.store
    ADD CONSTRAINT store_manager_staff_id_fkey FOREIGN KEY (manager_staff_id)
    REFERENCES public.staff (staff_id) MATCH SIMPLE
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
CREATE INDEX IF NOT EXISTS idx_unq_manager_staff_id
    ON public.store(manager_staff_id);

END;
```
## **Solving Business Problems**

### Solutions Implemented:

```sql
/* 1. Name the top 5 actors that are in movies with the most rentals.  How much revenue did each actor generate in total.  
Out of that total, get the percentage of total each actor contributed to total revenue.  
For the Actor who has the highest percentage give He or She the title "Best Selling Actor”*/

-- join tables and get the number of orders for each actor and the amount generated from each actor in sales
WITH actor_count AS (
SELECT A.actor_id, CONCAT(A.first_name,' ' ,A.last_name ) full_name, A.last_name, COUNT(A.actor_id) as actor_cnt, SUM(amount) actor_revenue
FROM customer as C INNER JOIN rental as R ON C.customer_id = R.customer_id
INNER JOIN inventory as I on I.inventory_id = R.inventory_id
INNER JOIN film_actor as FA ON FA.film_id = I.film_id INNER JOIN 
actor as A ON A.actor_id = FA.actor_id INNER JOIN payment as P ON
R.rental_id = P.rental_id
GROUP BY A.actor_id, A.first_name, A.last_name),   

--Rank actors based on number of rentals by customers and get the total revenue of all actors
ranking AS(
SELECT full_name, actor_cnt, actor_revenue, RANK() OVER( ORDER BY actor_cnt DESC) as actor_rank 
FROM actor_count),

--get the top 5 actors and total_revenue
total_revenue AS (
SELECT full_name, actor_cnt, actor_revenue, SUM(actor_revenue) OVER() AS total_revenue
FROM ranking
WHERE actor_rank <=5)

--get the percent of total for total revenue and get the rank of each actor
SELECT  full_name, actor_cnt, actor_revenue, CONCAT(ROUND((actor_revenue*100/total_revenue)::decimal,2),'%') as percent_of_total,
CASE WHEN RANK() OVER(ORDER BY (actor_revenue*100/total_revenue) DESC) = 1 THEN 'Best Selling Actor'
WHEN RANK() OVER(ORDER BY (actor_revenue*100/total_revenue) DESC) = 2 THEN 'Second Best Selling Actor'
WHEN RANK() OVER(ORDER BY (actor_revenue*100/total_revenue) DESC) = 3 THEN 'Third Best Selling Actor'
WHEN RANK() OVER(ORDER BY (actor_revenue*100/total_revenue) DESC) = 4 THEN 'Fourth Best Selling Actor'
WHEN RANK() OVER(ORDER BY (actor_revenue*100/total_revenue) DESC) = 5 THEN 'Fifth Best Selling Actor'
END as selling_rank
FROM total_revenue


SELECT *
FROM rental
ORDER BY rental_date
```

/* 2.
Calculate the monthly percentage change of total revenue over the last 5 months for each category.
Retrieve the month for each category that had the highest month-over-month percentage change.
*/
```sql
--filter data to last 5 months, get the total revenue for each category and previous month revenue
WITH cte AS(
SELECT CA.category_id as category_id, name as Category_name, 
TO_CHAR(payment_date, 'Month') as month, SUM(amount) as Total_revenue,
EXTRACT(month FROM payment_date) as month_num, 
LAG(SUM(amount)) OVER(PARTITION BY name order by EXTRACT(month FROM payment_date)) previous_amount
FROM inventory as I INNER JOIN rental as R
ON I.inventory_id = R.inventory_id INNER JOIN payment as P
ON P.rental_id = R.rental_id INNER JOIN film_category as FA
ON FA.film_id = I.film_id INNER JOIN category as CA on
FA.category_id = CA.category_id
WHERE payment_date BETWEEN 
(SELECT max(payment_date) FROM payment) - INTERVAL '5 MONTHS' AND 
(SELECT max(payment_date) FROM payment) 
GROUP BY CA.category_id, name, TO_CHAR(payment_date, 'Month'), EXTRACT(month FROM payment_date)),

--get the month over month percentage of each category and rank the month over month percentage for each month
ranking as (
SELECT category_name, month,month_num,SUM(Total_revenue) as Total_revenue, 
 COALESCE(SUM(previous_amount),0) previous_amount,
 COALESCE((SUM(total_revenue)-SUM(previous_amount))/SUM(previous_amount)*100,0) percentage_change_over_time,
 RANK() OVER(PARTITION BY category_name ORDER BY (SUM(total_revenue)-SUM(previous_amount))/SUM(previous_amount)*100 DESC) 
FROM cte
GROUP BY category_id,category_name,month, month_num
ORDER BY category_name, month_num)

--select the month with the highest month over month percentage change
SELECT category_name,month, 
CONCAT('$',Total_revenue) as Total_revenue, CONCAT('$',previous_amount) as Previous_amount, 
CONCAT(ROUND(percentage_change_over_time::decimal,2), '%') as percentage_change_over_time
FROM ranking
WHERE rank = 2
 ```
/* 3.
What is the average duration of all DVD's that were rented for each rating.
If the Average duration is above a hour and a half say "Customers prefer extended movies"
if below an hour and a half say "Customers prefer short films"
*/
```sql
--get the avg duration of films for each rating
WITH rating AS(
SELECT rating, AVG(length/60) as avg_duration_of_film
FROM film as F INNER JOIN inventory as I  
ON F.film_id = I.film_id INNER JOIN rental as R 
ON I.inventory_id = R.inventory_id INNER JOIN 
payment as P ON R.rental_id = P.rental_id
GROUP BY rating)

--determine if customers prefer short films or extended films for each rating
SELECT  rating, ROUND(avg_duration_of_film,3) as avg_duration_of_film,
CASE WHEN avg_duration_of_film < 1.5 THEN 'Customers prefer short films' ELSE 'Customers prefer extended movies' END
as customers_preference
FROM rating
```
/* 4.
Create a pivot table that show the months in columns and the stores in rows.  
The values within the columns should reflect the total payment amount of each month.
*/
```sql
--retrieve the columns from the table stores you created.  Add all the months together to get the total amount of each store.
SELECT
	stores.store_id, 
	CONCAT('$',SUM(January)) as January,
	CONCAT('$',SUM(February)) as February,
	CONCAT('$',SUM(March))    as March,
	CONCAT('$',SUM(April))    as April,
	CONCAT('$',SUM(May))      as May,
	CONCAT('$',SUM(January)+SUM(February)+SUM(March)+SUM(April)+SUM(May)) as Total
FROM
--get the total payment amount for each month, name each date number by the month date name, group by store_id
(SELECT 
ST.store_id,
CASE WHEN EXTRACT(MONTH FROM payment_date) = 1 THEN SUM(amount) END as January,
CASE WHEN EXTRACT(MONTH FROM payment_date) = 2 THEN SUM(amount) END as February,
CASE WHEN EXTRACT(MONTH FROM payment_date) = 3 THEN SUM(amount) END as March,
CASE WHEN EXTRACT(MONTH FROM payment_date) = 4 THEN SUM(amount) END as April,
CASE WHEN EXTRACT(MONTH FROM payment_date) = 5 THEN SUM(amount) END as May
FROM customer as C INNER JOIN address as A
ON C.address_id = A.address_id INNER JOIN city as CI
ON A.city_id = CI.city_id INNER JOIN country as CO
ON CI.country_id = CO.country_id INNER JOIN payment as P
ON C.customer_id = P.customer_id INNER JOIN rental AS R
ON P.rental_id = R.rental_id INNER Join staff as S ON 
R.staff_id = S.staff_id INNER JOIN store as ST ON S.store_id = ST.store_id
GROUP BY ST.store_id, payment_date) as stores
GROUP BY stores.store_id
ORDER BY stores.store_id
```
/* 5.
Inventory Stock Alerts
Query products with stock levels below a certain threshold (e.g., less than 3 units).
Challenge: Include last restock date.  
*/
```sql
--count # of films by title in each store.  Find the last_update date of the inventory, filter table by stock less than 3
SELECT title,store_id, COUNT(I.film_id) as inventory_stock, MAX(I.last_update) as last_update
FROM inventory as I INNER JOIN film as F ON
I.film_id = F.film_id
GROUP BY store_id, title
HAVING COUNT(I.film_id) < 3
ORDER BY 2,3
```
/* 6.
 get the total amount of customers for each country whose country name start with either an 'A' or 'S'
 What country has the most customers?
*/
```sql
--get the count of customers with names that start with 'a' or 's', then rank countries by number of customers
WITH customers_by_country AS (
SELECT country, count(C.customer_id) count_of_customers, RANK() OVER(ORDER BY count(C.customer_id) DESC ) ranking
from customer as C INNER JOIN address as A 
ON C.address_id = A.address_id INNER JOIN city as CI
ON A.city_id = CI.city_id INNER Join country as CO
ON CI.country_id = CO.country_id
WHERE country LIKE 'A%' OR country LIKE 'S%'
GROUP BY country
ORDER BY country)
```


## **Learning Outcomes**

This project enabled me to:
- Design and implement a normalized database schema.
- Use advanced SQL techniques, including window functions, subqueries, joins, case statements, date functions, and more.
- Conduct in-depth business analysis using SQL.
- Optimize query performance and handle large datasets efficiently.

---

## **Conclusion**

This advanced SQL project successfully demonstrates my ability to solve real-world problems using structured queries. From analyzing top performing countries and stores to optimizing inventory and logistics, the project provides valuable insights into operational challenges and solutions.

By completing this project, I have gained a deeper understanding of how SQL can be used to tackle complex data problems and drive business decision-making.


