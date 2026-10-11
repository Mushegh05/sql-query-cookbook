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
Business insight: The top 8 tracks made $3.98 revenue, and the other 2 made $1.99 revenue, but keeping in mind that a lot of tracks have a $1.99 unit price, those 2 are just 2 random tracks from hundreds of sold tracks, so those are not really in top 10.

### 2. Top Revenue-Generating Genres
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

 Business insigth: The most amount of revenue comes from Rock genre tracks. The top 4 genres by revenue are far beyond the others, but notice that all tracks from those genres are priced $0.99 on average, which is relatively low, so the amount of revenue has some negative correlation with the unit_price.

### 3. Top Revenue-Generating Countries

**Question:** Which countries generate the most revenue, and how does average order size compare between them?

```sql
 SELECT 
    billing_country AS country, 
    SUM(total) AS revenue, 
    ROUND(AVG(total), 2) AS average_order_size 
FROM invoice 
GROUP BY country 
ORDER BY revenue DESC;
 ```

|country| revenue | average_order_size|
|---|---|---|
| USA | 523.06 | 5.75|
| Canada | 303.96 | 5.43|
| France | 195.10 | 5.57|
| Brazil | 190.10 | 5.43|
| Germany | 156.48 | 5.59|
| United Kingdom | 112.86 | 5.37|
| Czech Republic | 90.24 | 6.45|
| Portugal | 77.24 | 5.52|
| India | 75.26 | 5.79|
| Chile | 46.62 | 6.66|
| Ireland | 45.62 | 6.52|
| Hungary | 45.62 | 6.52|
| Austria | 42.62 | 6.09|
| Finland | 41.62 | 5.95|
| Netherlands | 40.62 | 5.80|
| Norway | 39.62 | 5.66|
| Sweden | 38.62 | 5.52|
| Argentina | 37.62 | 5.37|
| Belgium | 37.62 | 5.37|
| Poland | 37.62 | 5.37|
| Australia | 37.62 | 5.37|
| Italy | 37.62 | 5.37|
| Denmark | 37.62 | 5.37|
| Spain | 37.62 | 5.37|

Business insight: The most amount of revenue comes from USA. The average order sizes from all countries are close - from $5.37 to $6.66.

### 4. Top 5 customers by lifetime spend
**Question:** Who are the top 5 customers by lifetime spend, and which support rep manages each?

```sql
SELECT 
    (customer.first_name || ' ' || customer.last_name) AS customer_name,
    SUM(invoice.total) AS total_spent,
    (employee.first_name || ' ' || employee.last_name) AS support_rep_name 
FROM invoice 
JOIN customer ON invoice.customer_id = customer.customer_id 
JOIN employee ON employee.employee_id = customer.support_rep_id 
GROUP BY 
    customer.customer_id,
    customer.first_name,
    customer.last_name,
    employee.first_name,
    employee.last_name
ORDER BY total_spent DESC
LIMIT 5;
```
|customer_name | total_spent | support_rep_name |
|---|---|---|
 | Helena Holý | 49.62 | Steve Johnson |
 | Richard Cunningham | 47.62 | Margaret Park |
 | Luis Rojas | 46.62 | Steve Johnson |
 | Ladislav Kovács | 45.62 | Jane Peacock |
 | Hugh O'Reilly | 45.62 | Jane Peacock |

 Business insight: None of the customers have spent more than $50 and 2 of the Top 3 Steve Johnson's customers.

 ### 5. Employees' Customer Count and Managers.

 **Question:** How many customers does each employee support, and who's their manager?

 ```sql
 SELECT 
    (e1.first_name || ' ' || e1.last_name) AS employee_name,
    COUNT(customer.customer_id) AS customer_count,
    (e2.first_name || ' ' || e2.last_name) AS manager
FROM customer 
JOIN employee e1 ON customer.support_rep_id = e1.employee_id 
JOIN employee e2 ON e1.reports_to = e2.employee_id 
GROUP BY 
    e1.employee_id,
    e1.first_name,
    e1.last_name,
    e2.first_name,
    e2.last_name
ORDER BY customer_count DESC;
 ```
 | employee_name | customer_count | manager |
|---|---|---|
| Jane Peacock  | 21 | Nancy Edwards |
| Margaret Park | 20 | Nancy Edwards |
| Steve Johnson | 18 | Nancy Edwards |

Business insigth: All employees have almost equal amount of customers (about 20) and their manager is the same.

### 6. Artists Who Never Sold a Track.

**Question:** Which artists have never sold a single track?

```sql
SELECT 
    artist.name 
FROM artist 
WHERE artist.artist_id NOT IN 
(SELECT 
    artist.artist_id 
FROM invoice_line 
JOIN track ON invoice_line.track_id = track.track_id
JOIN album ON track.album_id = album.album_id
JOIN artist ON artist.artist_id = album.artist_id);
```
| name |
|---|
| Milton Nascimento & Bebeto |
| Azymuth |
| João Gilberto |
| Bebel Gilberto |
| Jorge Vercilo |
| Baby Consuelo |
| Ney Matogrosso |
| Luiz Melodia |
| Nando Reis |
| Pedro Luís & A Parede |
| Banda Black Rio |
| Fernanda Porto |
| Os Cariocas |
| A Cor Do Som |
| Kid Abelha |
| Sandra De Sá |
| Hermeto Pascoal |
| Barão Vermelho |
| Edson, DJ Marky & DJ Patife Featuring Fernanda Porto |
| Santana Feat. Dave Matthews |
| Santana Feat. Everlast |
| Santana Feat. Rob Thomas |
| Santana Feat. Lauryn Hill & Cee-Lo |
| Santana Feat. The Project G&B |
| Santana Feat. Maná |
| Santana Feat. Eagle-Eye Cherry |
| Santana Feat. Eric Clapton |
| Vinícius De Moraes & Baden Powell |
| Vinícius E Qurteto Em Cy |
| Vinícius E Odette Lara |
| Vinicius, Toquinho & Quarteto Em Cy |
| Motörhead & Girlschool |
| Peter Tosh |
| R.E.M. Feat. KRS-One |
| Simply Red |
| Whitesnake |
| Christina Aguilera featuring BigElf |
| Aerosmith & Sierra Leone's Refugee Allstars |
| Los Lonely Boys |
| Corinne Bailey Rae |
| Dhani Harrison & Jakob Dylan |
| Jackson Browne |
| Avril Lavigne |
| Big & Rich |
| Youssou N'Dour |
| Black Eyed Peas |
| Jack Johnson |
| Ben Harper |
| Snow Patrol |
| Matisyahu |
| The Postal Service |
| Jaguares |
| The Flaming Lips |
| Jack's Mannequin & Mick Fleetwood |
| Regina Spektor |
| Xis |
| Nega Gizza |
| Gustavo & Andres Veiga & Salazar |
| Rodox |
| Charlie Brown Jr. |
| Pedro Luís E A Parede |
| Los Hermanos |
| Mundo Livre S/A |
| Otto |
| Instituto |
| Nação Zumbi |
| DJ Dolores & Orchestra Santa Massa |
| Seu Jorge |
| Sabotage E Instituto |
| Stereo Maracana |
| Cake |
| Aisha Duo |
| Karsh Kale |
| Aaron Goldberg |
| Nicolaus Esterhazy Sinfonia |
| Alberto Turco & Nova Schola Gregoriana |
| Richard Marlow & The Choir of Trinity College, Cambridge |
| Anne-Sophie Mutter, Herbert Von Karajan & Wiener Philharmoniker |
| Hilary Hahn, Jeffrey Kahane, Los Angeles Chamber Orchestra & Margaret Batjer |
| Wilhelm Kempff |
| Academy of St. Martin in the Fields Chamber Ensemble & Sir Neville Marriner |
| Berliner Philharmoniker, Claudio Abbado & Sabine Meyer |
| Orchestre Révolutionnaire et Romantique & John Eliot Gardiner |
| Britten Sinfonia, Ivor Bolton & Lesley Garrett |
| Chicago Symphony Chorus, Chicago Symphony Orchestra & Sir Georg Solti |
| London Symphony Orchestra & Sir Charles Mackerras |
| Barry Wordsworth & BBC Concert Orchestra |
| Herbert Von Karajan, Mirella Freni & Wiener Philharmoniker |
| Luciano Pavarotti |
| Leonard Bernstein & New York Philharmonic |
| Boston Symphony Orchestra & Seiji Ozawa |
| Aaron Copland & London Symphony Orchestra |
| Ton Koopman |
| Sergei Prokofiev & Yuri Temirkanov |
| James Levine |
| Academy of St. Martin in the Fields, Sir Neville Marriner & William Bennett |
| Gustav Mahler |
| Choir Of Westminster Abbey & Simon Preston |
| Chor der Wiener Staatsoper, Herbert Von Karajan & Wiener Philharmoniker |
| Sir Georg Solti, Sumi Jo & Wiener Philharmoniker |
| Christopher O'Riley |
| Fretwork |
| Les Arts Florissants & William Christie |
| Charles Dutoit & L'Orchestre Symphonique de Montréal |
| Itzhak Perlman |
| Gerald Moore |
| Mela Tenenbaum, Pro Musica Prague & Richard Kapp |
| C. Monteverdi, Nigel Rogers - Chiaroscuro; London Baroque; London Cornett & Sackbu |
| Nash Ensemble |
| Philip Glass Ensemble |

Business insight: There are 110 artists out of 275, who didn't sell any tracks. That's a pretty big number.

### 7. Non-Purchased Tracks Percentage

**Question:** What percentage of the entire track catalog has never been purchased at all?

```sql
WITH unpurchased_count AS (
    SELECT 
        COUNT(*) AS unpurchased 
    FROM track 
    WHERE track_id NOT IN (
    SELECT 
        track_id 
    FROM invoice_line))
SELECT 
    ROUND(
        (
            (SELECT 
                unpurchased 
            FROM unpurchased_count)
            * 100.0 / COUNT(*)
        ), 
        2) 
        AS unpurchased_percentage 
        FROM track;
```

| unpurchased_percentage |
|---|
| 43.36 |

Business insight: About 43% of the tracks are not sold at all, Which means that about 57% of the tracks were sold, meaning more than half of the tracks were good enough.

### 8. The Most Tracks in a Playlist
**Question:** Which playlists contain the most tracks?

```sql
SELECT 
    playlist.name, 
    COUNT(*) AS number_of_tracks 
FROM playlist_track 
JOIN playlist ON playlist.playlist_id = playlist_track.playlist_id 
GROUP BY playlist_track.playlist_id, playlist.name 
ORDER BY number_of_tracks DESC
LIMIT 1;
```
| name | number_of_tracks |
|---|---|
| Music | 3290 |

Business insight: The playlist named "Music" contains 3290 tracks which is a huge number of tracks. Only around 200 tracks from the database are not included in this track.

### 9. Small Market Countries.
**Question:** Which countries have fewer than 3 customers (small/niche markets)?

```sql
SELECT 
    country, 
    COUNT(*) AS number_of_customers 
FROM customer 
GROUP BY country 
HAVING COUNT(*) < 3 
ORDER BY number_of_customers DESC;
```
| country | number_of_customers |
|---|---|
| India | 2 |
| Czech  | 2 |
| Portugal | 2 |
| Hungary | 1 |
| Belgium | 1 |
| Chile | 1 |
| Sweden | 1 |
| Argentina | 1 |
| Netherlands | 1 |
| Austria | 1 |
| Australia | 1 |
| Poland | 1 |
| Ireland | 1 |
| Denmark | 1 |
| Finland | 1 |
| Norway | 1 |
| Spain | 1 |
| Italy | 1 |

Business insight: The following countries have too little amount of customers, so the main focus is currently on other countries.

### 10. High-Cost Tracks.
**Question:** Which tracks are priced above the catalog average - and are they concentrated in specific genres?

```sql
SELECT 
    genre.name AS genre_name, 
    COUNT(*) AS high_cost_tracks_count 
    FROM track 
    JOIN genre ON track.genre_id = genre.genre_id 
    WHERE track.unit_price > (SELECT AVG(unit_price) FROM track)
    GROUP BY genre.genre_id, genre.name
    ORDER BY high_cost_tracks_count DESC;
```
| genre_name | high_cost_tracks_count|
|---|---|
| TV Shows | 93 |
| Drama | 64 |
| Sci Fi &  | 26 |
| Comedy | 17 |
| Science Fiction | 13 |

Business insight: TV Show, Drama, Sci Fi, Comedy, Science Fiction tracks generally cost more than the average track, so these are much more profitable (especially TV Show tracks).

### 11. Artist Albums Count and Revenue.
**Question:** For each artist, how many albums do they have and how much total revenue have they generated?

```sql
SELECT 
    artist.name AS artist_name, 
    COUNT(DISTINCT album.album_id) AS total_albums, 
    COALESCE(SUM(invoice_line.quantity * invoice_line.unit_price), 0) AS revenue 
FROM artist 
LEFT JOIN album ON artist.artist_id = album.artist_id 
LEFT JOIN track ON album.album_id = track.album_id 
LEFT JOIN invoice_line ON invoice_line.track_id = track.track_id 
GROUP BY artist.artist_id, artist.name 
ORDER BY revenue DESC;
```
| artist_name | total_albums | revenue|
|---|---|---|
| Iron Maiden | 21 | 138.60 |
| U2 | 10 | 105.93 |
| Metallica | 10 | 90.09 |
| Led Zeppelin | 14 | 86.13 |
| Lost | 4 | 81.59 |
| The Office | 3 | 49.75 |
| Os Paralamas Do Sucesso | 3 | 44.55 |
| Deep Purple | 11 | 43.56 |
| Faith No More | 4 | 41.58 |
| Eric Clapton | 2 | 39.60 |
| R.E.M. | 3 | 38.61 |
| Creedence Clearwater Revival | 2 | 36.63 |
| Queen | 3 | 36.63 |
| Battlestar Galactica (Classic) | 1 | 35.82 |
| Guns N' Roses | 3 | 35.64 |
| Titãs | 2 | 33.66 |
| Green Day | 2 | 32.67 |
| Pearl Jam | 5 | 31.68 |
| Kiss | 2 | 30.69 |
| Van Halen | 4 | 28.71 |
| Various Artists | 4 | 28.71 |
| Chico Buarque | 1 | 26.73 |
| Red Hot Chili Peppers | 3 | 26.73 |
| Heroes | 1 | 25.87 |
| Lenny Kravitz | 1 | 25.74 |
| Chico Science & Nação Zumbi | 2 | 24.75 |
| Battlestar Galactica | 2 | 23.88 |
| Ozzy Osbourne | 6 | 23.76 |
| Smashing Pumpkins | 2 | 23.76 |
| Tim Maia | 2 | 23.76 |
| Antônio Carlos Jobim | 2 | 21.78 |
| Foo Fighters | 4 | 21.78 |
| Cássia Eller | 2 | 20.79 |
| Caetano Veloso | 2 | 20.79 |
| Gilberto Gil | 3 | 19.80 |
| Spyro Gyra | 2 | 19.80 |
| The Who | 1 | 18.81 |
| The Rolling Stones | 3 | 17.82 |
| Amy Winehouse | 2 | 17.82 |
| Jamiroquai | 3 | 17.82 |
| Djavan | 2 | 17.82 |
| Legião Urbana | 2 | 16.83 |
| Miles Davis | 3 | 16.83 |
| The Tea Party | 2 | 16.83 |
| Gene Krupa | 1 | 16.83 |
| AC/DC | 2 | 15.84 |
| The Cult | 2 | 15.84 |
| Audioslave | 3 | 15.84 |
| Cidade Negra | 2 | 15.84 |
| Milton Nascimento | 2 | 14.85 |
| Soundgarden | 1 | 14.85 |
| Toquinho & Vinícius | 1 | 14.85 |
| The Black Crowes | 2 | 13.86 |
| O Terço | 1 | 12.87 |
| Elis Regina | 1 | 12.87 |
| Lulu Santos | 2 | 12.87 |
| James Brown | 1 | 11.88 |
| System Of A Down | 1 | 11.88 |
| Nirvana | 2 | 11.88 |
| Bruce Dickinson | 1 | 11.88 |
| Santana | 3 | 11.88 |
| Body Count | 1 | 10.89 |
| Marvin Gaye | 1 | 9.90 |
| Aerosmith | 1 | 9.90 |
| Frank Sinatra | 1 | 9.90 |
| Judas Priest | 1 | 9.90 |
| Olodum | 1 | 9.90 |
| Raul Seixas | 1 | 9.90 |
| Skank | 2 | 9.90 |
| Stone Temple Pilots | 1 | 9.90 |
| The Clash | 1 | 9.90 |
| Planet Hemp | 1 | 8.91 |
| R.E.M. Feat. Kate Pearson | 1 | 8.91 |
| Incognito | 1 | 8.91 |
| Black Sabbath | 2 | 8.91 |
| Passengers | 1 | 8.91 |
| Gonzaguinha | 1 | 8.91 |
| Zeca Pagodinho | 1 | 8.91 |
| Stevie Ray Vaughan & Double Trouble | 1 | 8.91 |
| Os Mutantes | 1 | 7.92 |
| Alanis Morissette | 1 | 7.92 |
| UB40 | 1 | 7.92 |
| Marisa Monte | 1 | 7.92 |
| Funk Como Le Gusta | 1 | 7.92 |
| Godsmack | 1 | 7.92 |
| Page & Plant | 1 | 7.92 |
| David Coverdale | 1 | 7.92 |
| Men At Work | 1 | 7.92 |
| House Of Pain | 1 | 7.92 |
| Jimi Hendrix | 1 | 7.92 |
| Black Label Society | 2 | 7.92 |
| Falamansa | 1 | 7.92 |
| Alice In Chains | 1 | 6.93 |
| Mötley Crüe | 1 | 6.93 |
| Def Leppard | 1 | 6.93 |
| O Rappa | 1 | 6.93 |
| The Police | 1 | 6.93 |
| Marcos Valle | 1 | 6.93 |
| Raimundos | 1 | 6.93 |
| Buddy Guy | 1 | 6.93 |
| JET | 1 | 6.93 |
| BackBeat | 1 | 5.94 |
| João Suplicy | 1 | 5.94 |
| Velvet Revolver | 1 | 5.94 |
| Motörhead | 1 | 5.94 |
| Rush | 1 | 5.94 |
| Jorge Ben | 1 | 5.94 |
| Mônica Marianno | 1 | 5.94 |
| Vinícius De Moraes | 1 | 5.94 |
| Ed Motta | 1 | 5.94 |
| Jota Quest | 1 | 5.94 |
| Accept | 2 | 4.95 |
| Marillion | 1 | 4.95 |
| Scorpions | 1 | 4.95 |
| Cláudio Zoli | 1 | 4.95 |
| Terry Bozzio, Tony Levin & Steve Stevens | 1 | 4.95 |
| Paul D'Ianno | 1 | 4.95 |
| Aquaman | 1 | 3.98 |
| Temple of the Dog | 1 | 3.96 |
| The Doors | 1 | 3.96 |
| Frank Zappa & Captain Beefheart | 1 | 3.96 |
| Joe Satriani | 1 | 3.96 |
| Chris Cornell | 1 | 3.96 |
| Billy Cobham | 1 | 3.96 |
| Apocalyptica | 1 | 3.96 |
| Dennis Chambers | 1 | 3.96 |
| Pink Floyd | 1 | 3.96 |
| Michael Tilson Thomas & San Francisco Symphony | 2 | 2.97 |
| Eugene Ormandy | 3 | 1.98 |
| Equale Brass Ensemble, John Eliot Gardiner & Munich Monteverdi Orchestra and Choir | 1 | 1.98 |
| Berliner Philharmoniker & Herbert Von Karajan | 3 | 1.98 |
| Emerson String Quartet | 1 | 1.98 |
| Academy of St. Martin in the Fields, Sir Neville Marriner & Thurston Dart | 1 | 1.98 |
| Chicago Symphony Orchestra & Fritz Reiner | 1 | 1.98 |
| Dread Zeppelin | 1 | 0.99 |
| Academy of St. Martin in the Fields, John Birch, Sir Neville Marriner & Sylvia  | 1 | 0.99 |
| Sir Georg Solti & Wiener Philharmoniker | 1 | 0.99 |
| Royal Philharmonic Orchestra & Sir Thomas Beecham | 1 | 0.99 |
| Calexico | 1 | 0.99 |
| Otto Klemperer & Philharmonia Orchestra | 1 | 0.99 |
| Yehudi Menuhin | 1 | 0.99 |
| Philharmonia Orchestra & Sir Neville Marriner | 1 | 0.99 |
| Michele Campanella | 1 | 0.99 |
| The 12 Cellists of The Berlin Philharmonic | 1 | 0.99 |
| Adrian Leaper & Doreen de Feis | 1 | 0.99 |
| Roger Norrington, London Classical Players | 1 | 0.99 |
| Academy of St. Martin in the Fields & Sir Neville Marriner | 1 | 0.99 |
| Kent Nagano and Orchestre de l'Opéra de Lyon | 1 | 0.99 |
| Julian Bream | 1 | 0.99 |
| Martin Roscoe | 1 | 0.99 |
| Göteborgs Symfoniker & Neeme Järvi | 1 | 0.99 |
| Scholars Baroque Ensemble | 1 | 0.99 |
| Yo-Yo Ma | 1 | 0.99 |
| English Concert & Trevor Pinnock | 2 | 0.99 |
| Luciana Souza/Romero Lubambo | 1 | 0.99 |
| The Posies | 1 | 0.99 |
| Habib Koité and Bamada | 1 | 0.99 |
| Maurizio Pollini | 1 | 0.99 |
| Berliner Philharmoniker & Hans Rosbaud | 1 | 0.99 |
| Emanuel Ax, Eugene Ormandy & Philadelphia Orchestra | 1 | 0.99 |
| Orchestra of The Age of Enlightenment | 1 | 0.99 |
| Felix Schmidt, London Symphony Orchestra & Rafael Frühbeck de Burgos | 1 | 0.99 |
| Edo de Waart & San Francisco Symphony | 1 | 0.99 |
| Antal Doráti & London Symphony Orchestra | 1 | 0.99 |
| The King's Singers | 1 | 0.99 |
| Academy of St. Martin in the Fields, Sir Neville Marriner & William Bennett| 0 | 0 |
| Gustav Mahler| 1 | 0 |
| Choir Of Westminster Abbey & Simon Preston| 1 | 0 |
| Chor der Wiener Staatsoper, Herbert Von Karajan & Wiener Philharmoniker| 1 | 0 |
| Sir Georg Solti, Sumi Jo & Wiener Philharmoniker| 1 | 0 |
| Christopher O'Riley| 1 | 0 |
| Fretwork| 1 | 0 |
| Les Arts Florissants & William Christie| 1 | 0 |
| Charles Dutoit & L'Orchestre Symphonique de Montréal| 1 | 0 |
| Itzhak Perlman| 1 | 0 |
| Santana Feat. Maná| 0 | 0 |
| Santana Feat. Eagle-Eye Cherry| 0 | 0 |
| Santana Feat. Eric Clapton| 0 | 0 |
| Santana Feat. The Project G&B| 0 | 0 |
| Santana Feat. Lauryn Hill & Cee-Lo| 0 | 0 |
| Santana Feat. Rob Thomas| 0 | 0 |
| Vinícius De Moraes & Baden Powell| 0 | 0 |
| Vinícius E Qurteto Em Cy| 0 | 0 |
| Vinícius E Odette Lara| 0 | 0 |
| Vinicius, Toquinho & Quarteto Em Cy| 0 | 0 |
| Santana Feat. Everlast| 0 | 0 |
| Santana Feat. Dave Matthews| 0 | 0 |
| Edson, DJ Marky & DJ Patife Featuring Fernanda Porto| 0 | 0 |
| Barão Vermelho| 0 | 0 |
| Hermeto Pascoal| 0 | 0 |
| Sandra De Sá| 0 | 0 |
| Gerald Moore| 1 | 0 |
| Kid Abelha| 0 | 0 |
| A Cor Do Som| 0 | 0 |
| Os Cariocas| 0 | 0 |
| Fernanda Porto| 0 | 0 |
| Motörhead & Girlschool| 0 | 0 |
| Banda Black Rio| 0 | 0 |
| Mela Tenenbaum, Pro Musica Prague & Richard Kapp| 1 | 0 |
| Pedro Luís & A Parede| 0 | 0 |
| Nando Reis| 0 | 0 |
| Peter Tosh| 0 | 0 |
| R.E.M. Feat. KRS-One| 0 | 0 |
| Luiz Melodia| 0 | 0 |
| Ney Matogrosso| 0 | 0 |
| Simply Red| 0 | 0 |
| C. Monteverdi, Nigel Rogers - Chiaroscuro; London Baroque; London Cornett & Sackbu| 1 | 0 |
| Baby Consuelo| 0 | 0 |
| Jorge Vercilo| 0 | 0 |
| Nash Ensemble| 1 | 0 |
| Bebel Gilberto| 0 | 0 |
| João Gilberto| 0 | 0 |
| Philip Glass Ensemble| 1 | 0 |
| Azymuth| 0 | 0 |
| Milton Nascimento & Bebeto| 0 | 0 |
| Whitesnake| 0 | 0 |
| Christina Aguilera featuring BigElf| 0 | 0 |
| Aerosmith & Sierra Leone's Refugee Allstars| 0 | 0 |
| Los Lonely Boys| 0 | 0 |
| Corinne Bailey Rae| 0 | 0 |
| Dhani Harrison & Jakob Dylan| 0 | 0 |
| Jackson Browne| 0 | 0 |
| Avril Lavigne| 0 | 0 |
| Big & Rich| 0 | 0 |
| Youssou N'Dour| 0 | 0 |
| Black Eyed Peas| 0 | 0 |
| Jack Johnson| 0 | 0 |
| Ben Harper| 0 | 0 |
| Snow Patrol| 0 | 0 |
| Matisyahu| 0 | 0 |
| The Postal Service| 0 | 0 |
| Jaguares| 0 | 0 |
| The Flaming Lips| 0 | 0 |
| Jack's Mannequin & Mick Fleetwood| 0 | 0 |
| Regina Spektor| 0 | 0 |
| Xis| 0 | 0 |
| Nega Gizza| 0 | 0 |
| Gustavo & Andres Veiga & Salazar| 0 | 0 |
| Rodox| 0 | 0 |
| Charlie Brown Jr.| 0 | 0 |
| Pedro Luís E A Parede| 0 | 0 |
| Los Hermanos| 0 | 0 |
| Mundo Livre S/A| 0 | 0 |
| Otto| 0 | 0 |
| Instituto| 0 | 0 |
| Nação Zumbi| 0 | 0 |
| DJ Dolores & Orchestra Santa Massa| 0 | 0 |
| Seu Jorge| 0 | 0 |
| Sabotage E Instituto| 0 | 0 |
| Stereo Maracana| 0 | 0 |
| Cake| 1 | 0 |
| Aisha Duo| 1 | 0 |
| Karsh Kale| 1 | 0 |
| Aaron Goldberg| 1 | 0 |
| Nicolaus Esterhazy Sinfonia| 1 | 0 |
| Alberto Turco & Nova Schola Gregoriana| 1 | 0 |
| Richard Marlow & The Choir of Trinity College, Cambridge| 1 | 0 |
| Anne-Sophie Mutter, Herbert Von Karajan & Wiener Philharmoniker| 1 | 0 |
| Hilary Hahn, Jeffrey Kahane, Los Angeles Chamber Orchestra & Margaret Batjer| 1 | 0 |
| Wilhelm Kempff| 1 | 0 |
| Academy of St. Martin in the Fields Chamber Ensemble & Sir Neville Marriner| 1 | 0 |
| Berliner Philharmoniker, Claudio Abbado & Sabine Meyer| 1 | 0 |
| Orchestre Révolutionnaire et Romantique & John Eliot Gardiner| 1 | 0 |
| Britten Sinfonia, Ivor Bolton & Lesley Garrett| 1 | 0 |
| Chicago Symphony Chorus, Chicago Symphony Orchestra & Sir Georg Solti| 1 | 0 |
| London Symphony Orchestra & Sir Charles Mackerras| 1 | 0 |
| Barry Wordsworth & BBC Concert Orchestra| 1 | 0 |
| Herbert Von Karajan, Mirella Freni & Wiener Philharmoniker| 1 | 0 |
| Luciano Pavarotti| 1 | 0 |
| Leonard Bernstein & New York Philharmonic| 1 | 0 |
| Boston Symphony Orchestra & Seiji Ozawa| 1 | 0 |
| Aaron Copland & London Symphony Orchestra| 1 | 0 |
| Ton Koopman| 1 | 0 |
| Sergei Prokofiev & Yuri Temirkanov| 1 | 0 |
| James Levine| 1 | 0 |

Business insight: There is a strong correlation between the albums number and the revenue.

### 12. Missing Composer tracks.
**Question: ** Which tracks are missing composer information (a data-quality report)?

```sql
SELECT 
    COUNT(*) AS tracks_without_composer 
    FROM track 
    WHERE composer IS NULL;
```
| tracks_without_composer |
|---|
| 977 |

As there are too many tracks with no composers, I displayed only 10 of them.

```sql
SELECT 
    name AS track_name 
FROM track 
WHERE composer IS NULL 
LIMIT 10;
```
                  track_name
-----------------------------------------------
 Desafinado
 Garota De Ipanema
 Samba De Uma Nota Só (One Note Samba)
 Por Causa De Você
 Ligia
 Fotografia
 Dindi (Dindi)
 Se Todos Fossem Iguais A Você (Instrumental)
 Falando De Amor
 Angela
(10 rows)

Business insight: There are 977 tracks out of roughly 3500 with no composers mentioned, which is a pretty big percentage of tracks. That means that this is not high quality data, and using the composers column can lead to misleading conclusions.

### 13. Longest and Shortest tracks in each genre
**Question:** What's the longest and shortest track in each genre?

```sql
SELECT 
    track.name AS track_name, 
    genre.name AS genre_name, 
    milliseconds 
    FROM track 
    JOIN genre ON track.genre_id = genre.genre_id 
    WHERE 
        (milliseconds, track.genre_id) IN (SELECT MAX(milliseconds), track.genre_id FROM track GROUP BY track.genre_id) OR
        (milliseconds, track.genre_id) IN (SELECT MIN(milliseconds), track.genre_id FROM track GROUP BY track.genre_id) 
    ORDER BY track.genre_id, milliseconds;
```
| track_name | genre_name | milliseconds |
|---|---|---|
| É Uma Partida De Futebol | Rock | 1071 |
| Dazed And Confused | Rock | 1612329 |
| Outra Vez | Jazz | 126511 |
| My Funny Valentine (Live) | Jazz | 907520 |
| The Hellion | Metal | 41900 |
| Rime of the Ancient Mariner | Metal | 816509 |
| Now Sports | Alternative &  | 4884 |
| Homecoming / The Death Of St. Jimmy / East 12th St. / Nobody Likes You / Rock And Roll Girlfriend / We're Coming Home  | Alternative &  | 558602 |
| Good Golly Miss Molly | Rock And Roll | 106266 |
| Slow Down | Rock And Roll | 163265 |
| Too Many Ways (Alternate) | Blues | 135053 |
| Talkin' 'Bout Women Obviously | Blues | 589531 |
| Mateus Enter | Latin | 33149 |
| Faroeste Caboclo | Latin | 543007 |
| Pensamento | Reggae | 173008 |
| Equal Rights Downpresser Man | Reggae | 366733 |
| I Heard Love Is Blind | Pop | 129666 |
| Amy Amy Amy (Outro) | Pop | 663426 |
| Casinha Feliz | Soundtrack | 32287 |
| Always Forever Now | Soundtrack | 383764 |
| Formosa | Bossa Nova | 137482 |
| Samba Da Bênção | Bossa Nova | 409965 |
| Love And Marriage | Easy Listening | 89730 |
| Mack The Knife | Easy Listening | 292075 |
| Intro- Churchill S Speech | Heavy Metal | 48013 |
| Dance Of Death | Heavy Metal | 516649 |
| Papa's Got A Brand New Bag Pt.1 | R&B/Soul | 127399 |
| Rehab (Hot Chip Remix) | R&B/Soul | 418293 |
| Chapa o Coco | Electronica/Dance | 143830 |
| Just Another Story | Electronica/Dance | 529684 |
| Demorou! | World | 39131 |
| I Ka Barra (Your Work) | World | 300605 |
| Commercial 1 | Hip Hop/Rap | 7941 |
| Se Liga | Hip Hop/Rap | 410409 |
| A Measure of Salvation | Science Fiction | 2563938 |
| Hero | Science Fiction | 2713755 |
| Michael's Birthday | TV Shows | 1237791 |
| Occupation / Precipice | TV Shows | 5286953 |
| Crossroads, Pt. 1 | Sci Fi & Fantasy | 2622622 |
| Greetings from Earth, Pt. 1 | Sci Fi & Fantasy | 2960293 |
| LOST Season 4 Trailer | Drama | 112712 |
| Through a Looking Glass | Drama | 5088838 |
| Product Recall | Comedy | 1268268 |
| The Job | Comedy | 2541875 |
| She'll Never Be Your Man | Alternative | 204078 |
| Reach Down | Alternative | 672773 |
| Étude 1, In C Major - Preludio (Presto) - Liszt | Classical | 51780 |
| Adagio for Strings from the String Quartet, Op. 11 | Classical | 596519 |
| Die Zauberflöte, K.620: "Der Hölle Rache Kocht in Meinem Herze" | Opera | 174813 |

### 14. High-Value Customer Report
**Question:** Which customers have spent more than the average customer total?

```sql
WITH customer_total AS (
    SELECT 
        invoice.customer_id, 
        (customer.first_name || ' ' || customer.last_name) AS customer_name, 
        SUM(invoice.total) AS total 
    FROM invoice 
    JOIN customer ON customer.customer_id = invoice.customer_id 
    GROUP BY invoice.customer_id, customer_name)
SELECT * 
FROM customer_total 
WHERE total > (SELECT AVG(total) FROM customer_total) 
ORDER BY total DESC;
```
| customer_id | customer_name | total |
|---|---|---|
| 6 | Helena Holý | 49.62 |
| 26 | Richard Cunningham | 47.62 |
| 57 | Luis Rojas | 46.62 |
| 45 | Ladislav Kovács | 45.62 |
| 46 | Hugh O'Reilly | 45.62 |
| 24 | Frank Ralston | 43.62 |
| 28 | Julia Barnett | 43.62 |
| 37 | Fynn Zimmermann | 43.62 |
| 7 | Astrid Gruber | 42.62 |
| 25 | Victor Stevens | 42.62 |
| 44 | Terhi Hämäläinen | 41.62 |
| 43 | Isabelle Mercier | 40.62 |
| 48 | Johannes Van der Berg | 40.62 |
| 5 | František Wichterlová | 40.62 |
| 34 | João Fernandes | 39.62 |
| 17 | Jack Smith | 39.62 |
| 22 | Heather Leacock | 39.62 |
| 20 | Dan Miller | 39.62 |
| 42 | Wyatt Girard | 39.62 |
| 1 | Luís Gonçalves | 39.62 |
| 4 | Bjørn Hansen | 39.62 |
| 3 | François Tremblay | 39.62 |

### 15. Most Revenue per Employee From his assigned Customers.

**Question:** Which employees' assigned customers have generated the most combined revenue?

```sql
SELECT 
    (employee.first_name || ' ' || employee.last_name) AS employee_name, 
    SUM(invoice.total) AS revenue 
FROM invoice 
LEFT JOIN customer ON customer.customer_id = invoice.customer_id 
LEFT JOIN employee ON employee.employee_id = customer.support_rep_id 
GROUP BY customer.support_rep_id, employee.first_name, employee.last_name 
ORDER BY revenue DESC;
```
| employee_name | revenue |
|---|---|
| Jane Peacock  |  833.04 |
| Margaret Park |  775.40 |
| Steve Johnson |  720.16 |

### 16. Common Media Types

**Question:** Which media types are most and least common across the catalog?

```sql
SELECT 
    media_type.name AS media_type, 
    COUNT(*) AS track_count 
    FROM track 
    LEFT JOIN media_type ON media_type.media_type_id = track.media_type_id 
    GROUP BY media_type.media_type_id, media_type.name 
    ORDER BY track_count DESC;
```
| media_type | track_count |
|---|---|
| MPEG audio file | 3034 |
| Protected AAC audio file | 237 |
| Protected MPEG-4 video file | 214 |
| AAC audio file | 11 |
| Purchased AAC audio file | 7 |

Business insight: MPEG audio file media type is the most common among all tracks - 3034 out of roughly 3500 tracks! The least common type is Purchased AAC audio file media type.

### 17. Countries with Above-Average Income.
**Question:** Which countries' average invoice total exceeds the global average invoice total?

```sql
WITH country_total AS (
    SELECT 
        billing_country, 
        SUM(total) AS total 
    FROM invoice 
    GROUP BY billing_country 
    ORDER BY total DESC)
 SELECT *
 FROM country_total 
 WHERE total > (SELECT AVG(total) FROM country_total);

```

| billing_country | total |
|---|---|
| USA | 523.06 |
| Canada | 303.96 |
| France | 195.10 |
| Brazil | 190.10 |
| Germany | 156.48 |
| United Kingdom | 112.86 |

Business insight: USA is ahead of all other countries with invoice total $523.06. The countries with above-average invoice total have invoice total of $112.86 to $523.06.

### 18. Finding Poor Customers.

**Question:** Which customers have never placed a single invoice over $10?

```sql
SELECT 
    (customer.first_name || ' ' || customer.last_name) AS customer_name 
FROM customer 
WHERE NOT EXISTS (SELECT 1 FROM invoice WHERE invoice.customer_id = customer.customer_id AND invoice.total > 10);
```
| customer_name |
|---|
(0 rows)

Business insight: All customers have spent more than $10.

### 19. Average Number of Tracks Per Album and Genre.
**Question:** What's the average number of tracks per album, broken down by genre?

```sql
SELECT 
    genre.name, 
    ROUND((CAST(COUNT(track.track_id) AS NUMERIC) / (SELECT COUNT(*) FROM album)), 3) AS avg_per_album 
FROM track 
LEFT JOIN genre ON track.genre_id = genre.genre_id 
GROUP BY genre.genre_id, genre.name 
ORDER BY avg_per_album;
```
| name | avg_per_album |
|---|---|
| Opera | 0.003 |
| Rock And Roll | 0.035 |
| Science Fiction | 0.037 |
| Bossa Nova | 0.043 |
| Comedy | 0.049 |
| Easy Listening | 0.069 |
| Sci Fi & Fantasy | 0.075 |
| World | 0.081 |
| Heavy Metal | 0.081 |
| Electronica/Dance | 0.086 |
| Hip Hop/Rap | 0.101 |
| Alternative | 0.115 |
| Soundtrack | 0.124 |
| Pop | 0.138 |
| Reggae | 0.167 |
| R&B/Soul | 0.176 |
| Drama | 0.184 |
| Classical | 0.213 |
| Blues | 0.233 |
| TV Shows | 0.268 |
| Jazz | 0.375 |
| Alternative & Punk | 0.957 |
| Metal | 1.078 |
| Latin | 1.669 |
| Rock | 3.738 |

Business insight: On average there are almost 4 Rock tracks in each album, which is more than any other genre.

### 20. Higher than Avgerage Price Genres.

**Question:** Which genres have an average track price above the overall catalog average?

```sql
SELECT * 
FROM (
    SELECT 
        genre.name AS genre, 
        ROUND(AVG(unit_price), 2) AS avg_unit_price 
    FROM track 
    LEFT JOIN genre ON track.genre_id = genre.genre_id 
    GROUP BY genre.genre_id, genre.name
    ) 
WHERE avg_unit_price > (SELECT AVG(unit_price) FROM track);
```
| genre | avg_unit_price |
|---|---|
| Comedy | 1.99 |
| TV Shows | 1.99 |
| Sci Fi & Fantasy | 1.99 |
| Science Fiction | 1.99 |
| Drama | 1.99 |