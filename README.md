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
(10 rows)

