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

### 2. Top Revenue-Generating genres
**Question:** Which genres bring in the most total revenue, and what's the average track price per genre?

```sql
SELECT 
    genre.name, 
    SUM(invoice_line.unit_price * invoice_line.quantity) AS total_revenue 
FROM track 
JOIN genre ON genre.genre_id = track.genre_id 
JOIN invoice_line ON track.track_id = invoice_line.track_id 
GROUP BY genre.genre_id, genre.name 
ORDER BY total_revenue DESC;
```
name | total_revenue
|---|---|
 Rock               |        826.65
 Latin              |        382.14
 Metal              |        261.36
 Alternative & Punk |        241.56
 TV Shows           |         93.53
 Jazz               |         79.20
 Blues              |         60.39
 Drama              |         57.71
 R&B/Soul           |         40.59
 Classical          |         40.59
 Sci Fi & Fantasy   |         39.80
 Reggae             |         29.70
 Pop                |         27.72
 Soundtrack         |         19.80
 Comedy             |         17.91
 Hip Hop/Rap        |         16.83
 Bossa Nova         |         14.85
 Alternative        |         13.86
 World              |         12.87
 Science Fiction    |         11.94
 Heavy Metal        |         11.88
 Electronica/Dance  |         11.88
 Easy Listening     |          9.90
 Rock And Roll      |          5.94