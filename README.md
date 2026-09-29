# SQL Query Cookbook: Chinook Database

A portfolio of 20 practical business queries and insights built on the Chinook SQLite database.

## Dataset Overview
The Chinook database represents a digital media store, including tables for artists, albums, media tracks, invoices, and customers.

---

## Queries & Business Insights

### 1. Top 10 Revenue-Generating Tracks
**Question:** Which 10 tracks generate the most revenue, and how much has each earned?

```sql
SELECT 
    track.track_id,
    track.name,
    SUM(invoice_line.quantity * invoice_line.unit_price) AS revenue
    FROM invoice_line
    JOIN track ON track.track_id = invoice_line.track_id
    GROUP BY track.track_id, track.name
    ORDER BY revenue DESC
    LIMIT 10;
```
| track_id | name | revenue ($) |
|---|---|---|
| 3177 | Hot Girl | 3.98 |
| 3250 | Pilot | 3.98 |
| 3223 | How to Stop an Exploding Man | 3.98 |
| 3200 | Gay Witch Hunt | 3.98 |
| 2832 | The Woman King | 3.98 |
| 2850 | The Fix | 3.98 |
| 3214 | Phyllis's Wedding | 3.98 |
| 2868 | Walkabout | 3.98 |
| 2900 | Exposé | 1.99 |
| 2864 | Orientation | 1.99 |
Business insight: This table shows the tracks which have made the most amount of revenue. The top 8 tracks made $3.98 revenue, and the other 2 made $1.99 revenue, but keeping in mind that a lot of tracks have a $1.99 unit price, those 2 are just 2 random tracks from hundreds of sold tracks, so those are not really in top 10.

### 2. Top Revenue-Generating genres
**Question:** Which genres bring in the most total revenue, and what's the average track price per genre?

```sql
 SELECT 
    genre.name AS genre_name, 
    SUM(invoice_line.unit_price * invoice_line.quantity) AS total_revenue, 
    ROUND(AVG(invoice_line.unit_price), 2) AS average_track_price 
FROM track 
JOIN genre ON genre.genre_id = track.genre_id 
JOIN invoice_line ON track.track_id = invoice_line.track_id 
GROUP BY genre.genre_id, genre.name 
ORDER BY total_revenue DESC;
 ```

|genre_name     | total_revenue | average_track_price|
|---|---|---|
 |Rock               |        826.65 |                0.99|
 |Latin              |        382.14 |                0.99|
 |Metal              |        261.36 |                0.99|
 |Alternative & Punk |        241.56 |                0.99|
 |TV Shows           |         93.53 |                1.99|
 |Jazz               |         79.20 |                0.99|
 |Blues              |         60.39 |                0.99|
 |Drama              |         57.71 |                1.99|
 |R&B/Soul           |         40.59 |                0.99|
 |Classical          |         40.59 |                0.99|
 |Sci Fi & Fantasy   |         39.80 |                1.99|
 |Reggae             |         29.70 |                0.99|
 |Pop                |         27.72 |                0.99|
 |Soundtrack         |         19.80 |                0.99|
 |Comedy             |         17.91 |                1.99|
 |Hip Hop/Rap        |         16.83 |                0.99|
 |Bossa Nova         |         14.85 |                0.99|
 |Alternative        |         13.86 |                0.99|
 |World              |         12.87 |                0.99|
 |Science Fiction    |         11.94 |                1.99|
 |Heavy Metal        |         11.88 |                0.99|
 |Electronica/Dance  |         11.88 |                0.99|
 |Easy Listening     |          9.90 |                0.99|
 |Rock And Roll      |          5.94 |                0.99|

 Business insigth: This table shows the revenues from sold tracks by genres. The most amount of revenue comes from Rock genre tracks. The top 4 genres by revenue are far beyond the others, but notice that all tracks from those genres are priced $0.99 on average, which is relatively low, so the amount of revenue has some negative correlation with the unit_price.
 