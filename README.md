# Spotify Database Analysis 🎵

## Overview
This project provides **data exploration and analysis** for Spotify music tracks using **SQL queries**. It categorizes queries based on difficulty levels to help users explore trends, statistics, and key insights from music streaming data.

## 📂 Database Schema
The dataset consists of a single table **`spotify`**, which contains the following attributes:
- **Artist and Track Information** (`artist`, `track`, `album`, `album_type`)
- **Musical Features** (`danceability`, `energy`, `loudness`, `speechiness`, `acousticness`, `instrumentalness`, `liveness`, `valance`, `tempo`)
- **Engagement Metrics** (`views`, `likes`, `comments`, `stream`, `official_video`)
- **Platform Analysis** (`most_played_on`, `licensed`)

---

## 🏆 SQL Queries by Difficulty

### 📘 Easy Level
Basic queries to retrieve fundamental insights:

1. **Tracks with over 1 billion streams**:
```sql
SELECT track FROM spotify WHERE stream > 1000000000;
```

2. **List all albums with respective artists**:
```sql
SELECT DISTINCT album, artist FROM spotify;
```

3. **Total number of comments for licensed tracks**:
```sql
SELECT SUM(comments) AS total_comments FROM spotify WHERE licensed = TRUE;
```
4. **Tracks belonging to album type ‘single’**:
```sql
SELECT * FROM spotify WHERE album_type LIKE 'single';
```

5. **Count total number of tracks by each artist**:
```sql
SELECT artist, COUNT(*) AS total_tracks FROM spotify GROUP BY   artist ORDER BY total_tracks DESC;
```

### 📗 Medium Level

More analytical queries: 

6. **Average danceability for each album**:
```sql
SELECT album, AVG(danceability) AS avg_danceability FROM spotify GROUP BY album ORDER BY avg_danceability DESC;
```

7. **Top 5 tracks with the highest energy**:
```sql
SELECT track, MAX(energy) FROM spotify GROUP BY track ORDER BY MAX(energy) DESC LIMIT 5;
```

8. **Tracks with views and likes where official video = TRUE**:
```sql
SELECT track, SUM(views) AS total_views, SUM(likes) AS total_likes FROM spotify WHERE official_video = TRUE GROUP BY track ORDER BY total_views DESC LIMIT 5;
```
9. **Total views for each album across all tracks:**
```sql
SELECT album, SUM(views) AS total_views FROM spotify GROUP BY album ORDER BY total_views DESC;
```

10. **Tracks streamed more on Spotify than YouTube**:
```sql
SELECT track FROM (
    SELECT track,
           COALESCE(SUM(CASE WHEN most_played_on = 'YouTube' THEN stream END), 0) AS youtube_streams,
           COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) AS spotify_streams
    FROM spotify
    GROUP BY track
) AS streaming_data
    WHERE spotify_streams > youtube_streams AND youtube_streams <> 0;
```

### 📕 Advanced Level
Complex queries utilizing window functions and `WITH` clauses: 

11. **Top 3 most-viewed tracks per artist using window functions**:
```sql
WITH ranking_artist AS (
    SELECT artist, track, 
    SUM(views) AS total_views,
           DENSE_RANK() OVER (
               PARTITION BY artist ORDER BY SUM(views) DESC
               ) AS rank
    FROM spotify
    GROUP BY artist, track
)
    SELECT * FROM ranking_artist WHERE rank <= 3;
```

12. **Tracks where the liveness score is above the average**:
```sql
SELECT track, artist, liveness FROM spotify WHERE liveness > (SELECT AVG(liveness) FROM spotify);
```

13. **Calculate the difference between highest and lowest energy values for each album**:
```sql
WITH energy_stats AS (
    SELECT album, 
    MAX(energy) AS highest_energy, 
    MIN(energy) AS lowest_energy
    FROM spotify GROUP BY album
)
SELECT 
album, 
highest_energy - lowest_energy AS energy_difference 
FROM energy_stats 
ORDER BY energy_difference 
DESC;
```


### 🔧 Installation & Usage- Clone the repository:
git clone https://github.com/SiddheshWalunj/SQL_Spotify_Database_Analysis.git
- Import spotify_db using a MySQL client.
- Run the SQL queries for data exploration.

## 🏷️ License
This project is licensed under the MIT License.

## 🧑‍💻 Author

**Mr. Siddhesh Walunj**  
GitHub: https://github.com/SiddheshWalunj
