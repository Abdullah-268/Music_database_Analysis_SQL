# Music_database_SQL

This repository contains a portfolio of SQL database scripts, including schema definition, sample data, and example queries. <br>

I have analyzed the data from the music database and answered 11 sample queries in this analysis. <br>

Sample queries: <br> 

Q.1 Who is the senior most employee based on job title? <br>
**Description**: This query returns the senior-most employee based on job title. <br>
**SQL**:
```sql
Select * From employee 
Order By levels Desc Limit 1  
```

Q.2 Which country has the most invoices? <br>
**Description**: This query returns the country having the most invoices. <br>
**SQL**:
```sql

Select Count(*) as c, billing_country 
From invoice 
Group By billing_country 
Order By c Desc
```


Q.3 What are the top 3 values of total invoices? <br>
**Description**: This query retrieves the top 3 values of total invoices. <br>
**SQL**:
```sql

Select total From invoices 
Orders By total Desc 
Limit 3
```

Q.4 Which city has the best customers? We would like to throw a promotional Music Festival in the city where we made the most money. Write a query that returns one city that has the highest sum of invoice totals. Return both the city name & sum of all invoice totals. <br>
**Description**: The query you provided aims to identify the city with the highest total sum of invoice amounts. <br>
**SQL**:
```sql

Select Sum(total) As invoice_total, billing_city 
From invoice
Group By billing_city 
Order By invoice_total Desc
```

Q.5 Who is the best customer? The customer who has spent the most money will be declared the best customer. <br>
**Description**: This query retrieves the data of the customer who has spent the most money. <br>
**SQL**:
```sql

Select customer.customer_id, customer.first_name, customer.last_name, 
Sum(invoice.total) as total 
From customer
Join invoice on customer.customer_id = invoice.customer_id 
Group By customer.customer_id 
Order By total Desc 
Limit 1 
```
Q.6 Write a query to return the email, first name, last name, & Genre of all Rock Music listeners. Return your list ordered alphabetically by email starting with A. <br>
**Description**: This will retrieve the first name, last name, & Genre of all Rock music listeners list ordered alphabetically by email starting with A. <br>
**SQL**
```sql

Select Distinct email,first_name, last_name
From customer
Join invoice On customer.customer_id = invoice.customer_id
Join invoice_line On invoice.invoice_id = invoice_line.invoice_id
Where track_id In(
  Select track_id From track
  Join genre On track.genre_id = genre.genre_id
  Where genre.name Like 'Rock'
)
Order By email;
```
Q.7 Let's invite the artists who have written the most rock music in our dataset. Write that returns the Artist name and total track count of the top 10 rock bands. <br>
**Description**: This will retrieve the Artist name and total crack count of the top 10 rock bands. <br>
**SQL**
```sql

Select artist.artist_id, artist.name,Count(artist.artist_id) As number_of_songs
From track
Join album On album.album_id = track.album_id
Join artist On artist.artist_id = album.artist_id
Join genre On genre.genre_id = track.genre_id
Where genre.name Like 'Rock'
Group By artist.artist_id
Order By number_of_songs Desc
Limit 10;
```
Q.8 Return all the track names that have a song length longer than the average song length. Return the names and milliseconds for each track. Order by the song length with the longest songs listed first. <br>
**Description*: This query will return the names and lengths (in milliseconds) of tracks that have a duration longer than the average duration of all songs in the dataset. The list will be ordered by song length, with the longest songs listed first. <br>
**SQL**
```sql

Select name,milliseconds
From track
Where milliseconds > (
  Select Avg(milliseconds) As avg_track_length
  From track)
Order By milliseconds Desc;
```
Q.9 Find how much amount spent by each Customer on artists? Write a query to return the customer name, artist name, and total spent. <br>
**Description**: This query will return the amount spent by each customer on artists. It will provide the customer's name, artist's name, and the total amount spent by that customer on that particular artist. <br>
**SQL**
```sql

With best_selling_artist As (
  Select artist.artist_id As artist_id, artist.name As artist_name,
  Sum(invoice_line.unit_price*invoice_line.quantity) As total_sales
  From invoice_line
  Join track On track.track_id = invoice_line.track_id
  Join album On album.album_id = track.album_id
  Join artist On artist.artist_id = album.artist_id
  Group By 1, 2
  Order By 3 Desc
  Limit 1
)
Select c.customer_id, c.first_name, c.last_name, bsa.artist_name,
Sum(il.unit_price*il.quantity) As amount_spent
From invoice
Join customer c On c.customer_id = invoice.customer_id
Join invoice_line il On il.invoice_id = invoice.invoice_id
Join track t On t.track_id = il.track_id
Join album alb On alb.album_id = t.album_id
Join best_selling_artist bsa On bsa.artist_id = alb.artist_id
Group By 1, 2, 3, 4
Order By 5 Desc;
```
Q.10 We want to find out the most popular music genre for each country. We determine the most popular genre as the genre with the highest amount of purchases. Write a query that returns each country along with the top genre. For countries where the maximum number of purchases is shared return all genres. <br>
**Description**: This query will return each country along with the most popular music genre(s). If there's a single genre with the highest amount of purchases in a country, it will return that genre. However, if multiple genres have the same maximum number of purchases in a country, it will return all of those genres. <br>
**SQL**
```sql

With popular_genre As
(
  Select Count(invoice_line.quantity) As purchases, customer.country, genre.name, genre.genre_id,
  Row_Number() Over(Partition By customer.country Order By Count(invoice_line.quantity) Desc) As Rowno
  From invoice_line
  Join invoice On invoice.invoice_id = invoice_line.invoice_id
  Join customer On customer.customer_id = invoice.customer_id
  Join track On track.track_id = invoice_line.track_id
  Join genre On genre.genre_id = track.genre_id
  Group By 2,3,4
)
Select * From popular_genre Where RowNo = 1;
```
Q.11 Write a query that determines the customer that has spent the most on music for each country. Write a query that returns the country along with the top customer and how much they spent. For countries where the top amount spent is shared, provide all customers who spent this amount. <br>
**Description**: This query will return the country along with the top customer(s) and how much they spent. For countries where the top amount spent is shared among multiple customers, it will provide all those customers who spent this amount. <br>
**SQL**
```sql

With Recursive
 customer_with_country As (
  Select customer.customer_id,first_name, last_name,billing_country,Sum(total) As total_spending
  From invoice
  Join customer On customer.customer_id = invoice.customer_id
  Group By 1,2,3,4
  Order by 2,3 Desc),

country_max_spending As(
  Select billing_country,Max(total_spending) As max_spending
  From customer_with_country
  Group By billing_country)

Select cc.billing_country, cc.total_spending, cc.first_name, cc.last_name, cc.customer_id
From customer_with_country cc
Join country_max_spending ms
On cc.billing_country = ms.billing_country
Where cc.total_spending = ms.max_spending
Order by 1;
```


