
# Project Summary

https://postgres.devmountain.com/

Use http://www.sqlteaching.com or http://www.sqlbolt.com  as resources for referencing on keywords you'll need.

## Practice joins

1. Get all invoices where the `UnitPrice` on the `InvoiceLine` is greater than $0.99.

	SELECT * FROM invoice 
	INNER JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
	WHERE unit_price > .99;

2. Get the `InvoiceDate`, customer `FirstName` and `LastName`, and `Total` from all invoices.

	SELECT invoice_date, customer.first_name, customer.last_name, invoice.total FROM invoice
	INNER JOIN customer ON customer.customer_id = invoice.customer_id;

3. Get the customer `FirstName` and `LastName` and the support rep's `FirstName` and `LastName` from all customers. 
    * Support reps are on the Employee table.

	SELECT customer.first_name, customer.last_name, employee.first_name AS employeefirst, employee.last_name AS employeelast FROM customer
	JOIN employee ON customer.support_rep_id = employee.employee_id;

4. Get the album `Title` and the artist `Name` from all albums.

	SELECT album.title, artist.name FROM album 
	JOIN artist ON album.artist_id = artist.artist_id;

5. Get all PlaylistTrack TrackIds where the playlist `Name` is Music.

	SELECT playlist_track.track_id FROM playlist_track
	JOIN playlist ON playlist.playlist_id = playlist_track.playlist_id
	WHERE playlist.name = 'Music';

6. Get all Track `Name`s for `PlaylistId` 5.

	SELECT name FROM track 
	JOIN playlist_track ON playlist_track.track_id = track.track_id
	WHERE playlist_id = 5;

7. Get all Track `Name`s and the playlist `Name` that they're on ( 2 joins ).

	SELECT track.name AS trackname, playlist.name AS playlistname FROM track 
	JOIN playlist_track ON playlist_track.track_id = track.track_id
	JOIN playlist ON playlist_track.playlist_id = playlist.playlist_id;


8. Get all Track `Name`s and Album `Title`s that are the genre `"Alternative"` ( 2 joins ).

	SELECT track.name, album.title FROM album
	JOIN track on track.album_id = album.album_id
	JOIN genre ON track.genre_id = genre.genre_id
	WHERE genre.name = 'Alternative';

9. Get all tracks on the playlist(s) called Music and show their name, genre name, album name, and artist name.
  * At least 5 joins.

	SELECT track.name AS track, genre.name AS genre, album.title AS album, artist.name AS artist FROM track 
	JOIN album ON album.album_id=track.album_id
	JOIN genre ON genre.genre_id = track.genre_id
	JOIN artist ON artist.artist_id = album.artist_id
	JOIN playlist_track ON playlist_track.track_id = track.track_id
	JOIN playlist ON playlist.playlist_id = playlist_track.playlist_id
	WHERE playlist.name = 'Music';


## Practice nested queries

Complete the instructions without using any joins. Only use nested queries to come up with the solution.

1. Get all invoices where the `UnitPrice` on the `InvoiceLine` is greater than $0.99.

	SELECT * FROM invoice WHERE invoice_id IN 
	(SELECT invoice_id FROM invoice_line WHERE unit_price > .99);

2. Get all Playlist Tracks where the playlist name is Music.

	SELECT * FROM playlist_track WHERE playlist_id IN
	(SELECT playlist_id FROM playlist WHERE name = 'music');

3. Get all Track names for `PlaylistId` 5.

	SELECT name FROM track WHERE track_id IN
	(SELECT track_id FROM playlist_track WHERE playlist_id = 5);

4. Get all tracks where the `Genre` is Comedy.

	SELECT * FROM track WHERE genre_id IN
	(SELECT genre_id FROM genre WHERE name = 'Comedy');

5. Get all tracks where the `Album` is Fireball.

	SELECT * FROM track WHERE album_id IN
	(SELECT album_id FROM album WHERE title = 'Fireball');

6. Get all tracks for the artist Queen ( 2 nested subqueries ).

	SELECT * FROM track WHERE album_id IN
	(SELECT album_id FROM album WHERE artist_id IN
	(SELECT artist_id FROM artist WHERE name = 'Queen'))

## Practice updating Rows

1. Find all customers with fax numbers and set those numbers to `null`.

	UPDATE customer
	SET fax = null
	WHERE fax IS NOT null;

2. Find all customers with no company (null) and set their company to `"Self"`.

	UPDATE customer
	SET company = '"Self"'
	WHERE company IS NULL;

3. Find the customer `Julia Barnett` and change her last name to `Thompson`.

	UPDATE customer
	SET last_name = 'Thompson'
	WHERE first_name = 'Julia' AND last_name = 'Barnett';

4. Find the customer with this email `luisrojas@yahoo.cl` and change his support rep to `4`.

	UPDATE customer
	SET support_rep_id = 4
	WHERE email = 'luisrojas@yahoo.cl';

5. Find all tracks that are the genre `Metal` and have no composer. Set the composer to `"The darkness around us"`.

	UPDATE track
	SET composer = '"The darkness around us"'
	WHERE genre_id IN(SELECT genre_id FROM genre WHERE name = 'Metal')
	AND composer IS NULL;

6. Refresh your page to remove all database changes.

	done :D

## Group by

1. Find a count of how many tracks there are per genre. Display the genre name with the count.

	SELECT genre.name, COUNT(*) FROM track 
	JOIN genre ON genre.genre_id = track.genre_id
	GROUP BY genre.name;

2. Find a count of how many tracks are the `"Pop"` genre and how many tracks are the `"Rock"` genre.

	SELECT genre.name, COUNT(*) FROM track 
	JOIN genre ON genre.genre_id = track.genre_id
	WHERE genre.name IN ('Pop','Rock')
	GROUP BY genre.name;

3. Find a list of all artists and how many albums they have.

	SELECT artist.name, COUNT(album.artist_id) FROM album
	JOIN artist ON artist.artist_id = album.artist_id
	GROUP BY artist.name;

## Use Distinct

1. From the `Track` table find a unique list of all `Composer`s.

	SELECT DISTINCT composer FROM track;

2. From the `Invoice` table find a unique list of all `BillingPostalCode`s.

	SELECT DISTINCT billing_postal_code FROM invoice;

3. From the `Customer` table find a unique list of all `Company`s.

	SELECT DISTINCT company FROM customer;

## Delete Rows


2. Delete all `"bronze"` entries from the table.


	DELETE FROM practice_delete
	WHERE type = 'bronze';

3. Delete all `"silver"` entries from the table.


	DELETE FROM practice_delete
	WHERE type = 'silver';

4. Delete all entries whose value is equal to `150`.

	DELETE FROM practice_delete
	WHERE value = 150;

## eCommerce Simulation - No Hints

Let's simulate an e-commerce site. We're going to need users, products, and orders.

* Users need a name and an email.
* Products need a name and a price
* Orders need a ref to product.
* All 3 need primary keys.

### Instructions

* Create 3 tables following the criteria in the summary.

	CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(200), email VARCHAR(200));
	CREATE TABLE products (id SERIAL PRIMARY KEY, name VARCHAR(200), price INTEGER);
	CREATE TABLE orders (id SERIAL PRIMARY KEY, product_id INTEGER REFERENCES products (id));


* Add some data to fill up each table.
  * At least 3 users, 3 products, 3 orders.

	INSERT INTO users (name, email)
	VALUES ('Eric','ew@aol.com'), ('Braden','BRDN@yahoo.com'), ('David','DBILEZ@hotmail.com');

	INSERT INTO products (name, price)
	VALUES ('Ice', 3), ('Hammer', 4), ('Waffle Cone', 100);

	INSERT INTO orders (product_id)
	VALUES (1), (1), (3);

* Run queries against your data.
  * Get all products for the first order.

	SELECT * FROM orders 
	WHERE id = 1;

  * Get all orders.

	SELECT * FROM orders;

  * Get the total cost of an order ( sum the price of all products on an order ).

	SELECT price FROM products WHERE id IN (SELECT product_id FROM orders WHERE id = 3);

* Add a foreign key reference from Orders to Users.

	ALTER TABLE orders
	ADD COLUMN user_id INTEGER REFERENCES users (id);

* Update the Orders table to link a user to each order.

	UPDATE orders
	SET user_id = 1
	WHERE id = 1;
	
	UPDATE orders
	SET user_id = 2
	WHERE id = 3;

	UPDATE orders
	SET user_id = 3
	WHERE id = 2;

* Run queries against your data.
  * Get all orders for a user.

	SELECT * FROM orders WHERE user_id = 2;

  * Get how many orders each user has.

	SELECT user_id, COUNT(*) FROM orders 
	GROUP BY user_id;

* Get the total amount on all orders for each user.

	SELECT users.name, products.price FROM users
	JOIN orders ON orders.user_id = users.id
	JOIN products ON orders.product_id = products.id
	GROUP BY users.id, products.id;