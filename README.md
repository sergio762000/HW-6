# HW-6

Создание структуры БД для кинотеатра + выборка самого доходного фильма

```
CREATE TABLE IF NOT EXISTS cinema (
	id		serial PRIMARY KEY,
	name		varchar	 NOT NULL,
	address		varchar NOT NULL,
	created_at	timestamp NOT NULL,
	updated_at	timestamp,
	deleted_at	timestamp
);

CREATE TABLE IF NOT EXISTS film (
	id 		serial PRIMARY KEY,
	name 		varchar	NOT NULL,
	description 	varchar	NOT NULL,
	duration	int DEFAULT(0),
	base_price	int DEFAULT (0),
	created_at	timestamp NOT NULL,
	updated_at	timestamp,
	deleted_at	timestamp
);

CREATE TABLE IF NOT EXISTS category_hall (
	id 		serial PRIMARY KEY,
	name 		varchar	NOT NULL,
	description 	varchar	NOT NULL,
	margin_amount	int DEFAULT(0),
	created_at	timestamp NOT NULL,
	updated_at	timestamp,
	deleted_at	timestamp
);

CREATE TABLE IF NOT EXISTS category_seats (
	id 		serial PRIMARY KEY,
	name 		varchar	NOT NULL,
	description 	varchar	NOT NULL,
	margin_amount	int DEFAULT(0),
	created_at	timestamp NOT NULL,
	updated_at	timestamp,
	deleted_at	timestamp
);

CREATE TABLE IF NOT EXISTS category_session (
	id 		serial PRIMARY KEY,
	name 		varchar	NOT NULL,
	description 	varchar	NOT NULL,
	margin_amount	int DEFAULT(0),
	created_at	timestamp NOT NULL,
	updated_at	timestamp,
	deleted_at	timestamp
);

CREATE TABLE IF NOT EXISTS hall (
    id			serial PRIMARY KEY,
    name		varchar	NOT NULL,
    quantity_seats	int NOT NULL CHECK (quantity_seats > 0),
    category_hall_id	bigint REFERENCES category_hall (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
    cinema_id		bigint REFERENCES cinema (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
    created_at		timestamp NOT NULL,
    updated_at		timestamp,
    deleted_at		timestamp
);

CREATE TABLE IF NOT EXISTS seats (
	id			serial PRIMARY KEY,
	name			varchar	NOT NULL,
	category_seats_id	bigint REFERENCES category_seats (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	hall_id			bigint REFERENCES hall (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	is_free			boolean NOT NULL DEFAULT (true),
	created_at		timestamp NOT NULL,
	updated_at		timestamp,
	deleted_at		timestamp
);

CREATE TABLE IF NOT EXISTS session_film (
	id			serial PRIMARY KEY,
	cinema_id		bigint REFERENCES cinema (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	hall_id			bigint REFERENCES hall (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	film_id			bigint REFERENCES film (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	category_session_id 	bigint REFERENCES category_session (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	start_time		timestamp NOT NULL,
	finish_time		timestamp NOT NULL,
	created_at		timestamp NOT NULL,
	updated_at		timestamp,
	deleted_at		timestamp
);

CREATE TABLE IF NOT EXISTS ticket (
	id		serial PRIMARY KEY,
	session_id	bigint REFERENCES session_film (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	seats_id	bigint REFERENCES seats (id) ON DELETE RESTRICT ON UPDATE NO ACTION,
	price		int NOT NULL,
	created_at	timestamp NOT NULL,
	deleted_at	timestamp
);


SELECT DISTINCT
    sum(t.price) over (partition by f.id)    	AS total_cash,
    f.name  					AS "Наименование фильма"
FROM ticket t
    JOIN public.session_film sf on t.session_id = sf.id
    JOIN public.film f on sf.film_id = f.id
WHERE t.deleted_at IS NULL
    AND sf.deleted_at IS NULL
    AND f.deleted_at IS NULL
ORDER BY total_cash desc
LIMIT 1
```
