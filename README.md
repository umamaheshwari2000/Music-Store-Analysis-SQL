<div align="center">

<!-- HEADER BANNER -->

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12&height=200&section=header&text=🎵%20Music%20Store%20Analysis&fontSize=42&fontAlignY=38&desc=SQL%20%7C%20Data%20Analysis%20%7C%20Business%20Intelligence&descAlignY=58&descAlign=50&fontColor=ffffff" width="100%"/>

<!-- BADGES -->

<p>
  <img src="https://img.shields.io/badge/SQL-PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white"/>
  <img src="https://img.shields.io/badge/Analysis-Business%20Intelligence-FF6B6B?style=for-the-badge&logo=chartdotjs&logoColor=white"/>
  <img src="https://img.shields.io/badge/Level-Easy%20%7C%20Moderate%20%7C%20Advanced-gold?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Completed-4CAF50?style=for-the-badge&logo=checkmarx&logoColor=white"/>
</p>

<br/>


> *“Turning raw data into business decisions — one query at a time.”*

</div>

-----

## 🎯 Project Overview

This project is a **complete end-to-end SQL analysis** of a digital music store database. By dissecting real-world transactional data, it answers critical business questions across three difficulty tiers — from basic aggregations to complex window functions — helping the store unlock insights about customers, revenue, artists, and global music trends.

Whether you’re a data enthusiast or a recruiter evaluating SQL depth, this project showcases **practical, business-driven querying skills** built on a normalized relational schema.



## 🗂️ Database Schema

The analysis is performed on a **Music Playlist Database** with 11 interconnected tables:

employee ──── customer ──── invoice ──── invoice_line
                                              │
                                           track ──── album ──── artist
                                              │
                                          genre   media_type
                                              
playlist ──── playlist_track ──── track


|Table           |Description                                   |
|----------------|----------------------------------------------|
|`employee`      |Staff hierarchy with job titles and levels    |
|`customer`      |Customer demographics and contact details     |
|`invoice`       |Billing records with city, country, and totals|
|`invoice_line`  |Line-item breakdown of each invoice           |
|`track`         |Song metadata — duration, price, genre, album |
|`album`         |Album titles linked to artists                |
|`artist`        |Artist names and IDs                          |
|`genre`         |Music genre classification                    |
|`media_type`    |Format type (MP3, AAC, etc.)                  |
|`playlist`      |Named playlists                               |
|`playlist_track`|Many-to-many: playlists ↔ tracks              |



## 🧩 Question Breakdown by Difficulty

<div align="center">

|🟢 Easy               |🟡 Moderate               |🔴 Advanced                                 |
|---------------------|-------------------------|-------------------------------------------|
|SELECT, GROUP BY     |JOINs across tables      |CTEs (Common Table Expressions)            |
|ORDER BY, LIMIT, DESC|Aggregations with filters|Window Functions (ROW_NUMBER, PARTITION BY)|
|Single-table queries |Multi-table joins        |Nested CTEs + Subqueries                   |


</div>


## 🟢 EASY — Foundational Queries

### ♟️ Q1 · Senior-Most Employee by Job Title

> **Business Goal:** Identify the top of the organizational hierarchy.

```sql
SELECT title, first_name, last_name, levels
FROM employee
ORDER BY levels DESC
LIMIT 1;


✦ **Result:** `Andrew Adams` — *General Manager* at Level **L6**

> 🔍 **Insight:** Used `ORDER BY levels DESC` to rank seniority. Clean and efficient — no joins needed when the answer lives in a single tabl



### 🌍 Q2 · Countries with the Most Invoices

> **Business Goal:** Discover where the store’s sales volume is highest.

```sql
SELECT billing_country AS Country, COUNT(*) AS Invoices
FROM invoice
GROUP BY Country
ORDER BY Invoices DESC
LIMIT 1;
```

✦ **Result:** 🇺🇸 **USA** — **131 invoices** (largest customer base)

> 🔍 **Insight:** The USA dominates invoice count, making it the primary market for targeted promotions and customer retention strategies.



### 💰 Q3 · Top 3 Highest Invoice Values

> **Business Goal:** Surface the biggest individual transactions.

```sql
SELECT invoice_id, total AS total_invoice
FROM invoice
ORDER BY total DESC
LIMIT 3;
```

✦ **Result:**

|Invoice ID|Total |
|----------|------|
|183       |$23.76|
|92        |$19.80|
|31        |$19.80|


> 🔍 **Insight:** The top invoice nearly doubles the runner-up — worth investigating what drove that single purchase spike.


### 🏙️ Q4 · Best City for a Music Festival

> **Business Goal:** Identify the city generating the most revenue to host a promotional event.

```sql
SELECT billing_city, SUM(total) AS invoice_total
FROM invoice
GROUP BY billing_city
ORDER BY invoice_total DESC
LIMIT 1;
```

✦ **Result:** 🎵 **Prague** — **$273.24** in total invoice revenue

> 🔍 **Insight:** Prague leads all cities in cumulative spend — the ideal location to throw a Music Festival and reward the store’s best market.



### 👑 Q5 · Best Customer (Highest Spender)

> **Business Goal:** Reward the most loyal, high-value customer.

```sql
SELECT customer.customer_id, first_name, last_name, SUM(total) AS Total_Spending
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id, first_name, last_name
ORDER BY Total_Spending DESC
LIMIT 1;
```

✦ **Result:** Customer **#5** — **$144.54** total spent

> 🔍 **Insight:** This customer is the store’s MVP — a prime candidate for VIP loyalty rewards or early-access promotions.



## 🟡 MODERATE — Multi-Table Join Queries

### 🎸 M1 · Rock Music Listeners (Alphabetical Email List)

> **Business Goal:** Build a targeted outreach list for a Rock genre campaign.

```sql
SELECT DISTINCT email AS Email, first_name AS FirstName,
       last_name AS LastName, genre.name AS Name
FROM customer
JOIN invoice ON invoice.customer_id = customer.customer_id
JOIN invoice_line ON invoice_line.invoice_id = invoice.invoice_id
JOIN track ON track.track_id = invoice_line.track_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
ORDER BY email;
```

✦ **Result:** A deduplicated list of Rock fans ordered A → Z by email

> 🔍 **Insight:** 4-table JOIN chain + `SELECT DISTINCT` ensures clean, non-duplicate contact data. This list powers a precision email campaign targeting verified Rock buyers.



### 🎤 M2 · Top 10 Rock Bands by Track Count

> **Business Goal:** Invite the most prolific Rock artists to a store event.

```sql
SELECT artist.name AS Artist_Name, COUNT(track.track_id) AS Total_track
FROM artist
JOIN album ON album.artist_id = artist.artist_id
JOIN track ON track.album_id = album.album_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
GROUP BY Artist_Name
ORDER BY Total_track DESC
LIMIT 10;
```

✦ **Top Results:**

|Artist           |Rock Tracks|
|-----------------|-----------|
|AC/DC            |18         |
|Aerosmith        |15         |
|Audioslave       |14         |
|Led Zeppelin     |14         |
|Alanis Morissette|13         |


> 🔍 **Insight:** AC/DC dominates the Rock catalog — a clear headliner choice for any promotional Rock music event.



### ⏱️ M3 · Tracks Longer Than Average Duration

> **Business Goal:** Curate a “deep cuts” playlist of extended tracks.

```sql
SELECT name AS Track_Name, milliseconds
FROM track
WHERE milliseconds > (
    SELECT AVG(milliseconds) AS avg_track_length FROM track
)
ORDER BY milliseconds DESC;
```

✦ **Top Long Tracks:**

|Track              |Duration  |
|-------------------|----------|
|How Many More Times|711,836 ms|
|Advance Romance    |677,694 ms|
|Sleeping Village   |644,571 ms|


> 🔍 **Insight:** Correlated subquery in `WHERE` clause — calculates the global average inline and filters dynamically. Efficient and elegant.



## 🔴 ADVANCED — CTEs & Window Functions

### 💎 A1 · Customer Spend Per Artist (Best Selling Artist Focus)

> **Business Goal:** Understand which artist drives the most revenue per customer.

```sql
WITH best_selling_artist AS (
    SELECT artist.artist_id, artist.name AS artist_name,
           SUM(invoice_line.unit_price * invoice_line.quantity) AS Total_Sales
    FROM invoice_line
    JOIN track ON track.track_id = invoice_line.track_id
    JOIN album ON album.album_id = track.album_id
    JOIN artist ON artist.artist_id = album.artist_id
    GROUP BY 1,2
    ORDER BY 3 DESC
    LIMIT 1
)
SELECT c.customer_id, c.first_name, c.last_name, bsa.artist_name,
       ROUND(SUM(il.unit_price * il.quantity), 2) AS Total_Spent
FROM invoice i
JOIN customer c ON c.customer_id = i.customer_id
JOIN invoice_line il ON il.invoice_id = i.invoice_id
JOIN track t ON t.track_id = il.track_id
JOIN album alb ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY 1,2,3,4
ORDER BY 5 DESC;
```

✦ **Result:** **AC/DC** is the best-selling artist. **Steve Murray** leads spending at **$17.82**

> 🔍 **Insight:** CTE pre-computes the #1 artist, then the main query joins all customer purchases filtered to that artist — a powerful two-stage pattern for drill-down analysis.


### 🌐 A2 · Most Popular Genre Per Country

> **Business Goal:** Tailor genre-based playlists and promotions by region.

```sql
WITH most_popular_genre AS (
    SELECT customer.country AS Country,
           COUNT(invoice_line.quantity) AS Purchases,
           genre.genre_id AS Genre_ID,
           genre.name AS Genre_Name,
           ROW_NUMBER() OVER(PARTITION BY customer.country 
                             ORDER BY COUNT(invoice_line.quantity) DESC) AS Row_No
    FROM customer
    JOIN invoice ON invoice.customer_id = invoice.invoice_id
    JOIN invoice_line ON invoice_line.invoice_id = invoice.invoice_id
    JOIN track ON track.track_id = invoice_line.track_id
    JOIN genre ON genre.genre_id = track.genre_id
    GROUP BY Country, genre.genre_id, genre.name
    ORDER BY Country, Purchases DESC
)
SELECT Country, Purchases, Genre_id, Genre_Name
FROM most_popular_genre WHERE Row_No <= 1;
```

✦ **Key Results:**

|Country|Top Genre|Purchases|
|-------|---------|---------|
|Canada |Rock     |57       |
|Germany|Rock     |28       |
|Brazil |Rock     |26       |
|Norway |Metal    |2        |


> 🔍 **Insight:** `ROW_NUMBER() OVER (PARTITION BY country)` is the hallmark of advanced SQL — ranking genres within each country without a self-join. Rock dominates globally; Norway uniquely prefers Metal.



### 🏆 A3 · Top Spending Customer Per Country

> **Business Goal:** Identify the #1 customer in each country for localized VIP treatment.

```sql
WITH Customer_with_country AS (
    SELECT customer.customer_id, customer.first_name, customer.last_name,
           invoice.billing_country AS Country,
           ROUND(SUM(invoice.total), 2) AS total_spent,
           ROW_NUMBER() OVER(PARTITION BY invoice.billing_country 
                             ORDER BY SUM(invoice.total) DESC) AS Row_No
    FROM customer
    JOIN invoice ON invoice.customer_id = customer.customer_id
    GROUP BY 1,2,3,4
    ORDER BY 4,5 DESC
)
SELECT customer_id, first_name, last_name, Country, total_spent
FROM Customer_with_country
WHERE Row_No <= 1;
```

✦ **Highlights:**

|Customer             |Country       |Total Spent|
|---------------------|--------------|-----------|
|František Wichterlová|Czech Republic|$144.54    |
|Manoj Pareek         |India         |$111.87    |
|Luís Gonçalves       |Brazil        |$108.90    |


> 🔍 **Insight:** Same `ROW_NUMBER() OVER (PARTITION BY country)` technique — now applied to customer spending. Returns exactly one top spender per country (or all tied customers when spend is equal).



## 📊 Key Business Insights Summary

```
┌─────────────────────────────────────────────────────────────────┐
│  🏆  Best Customer        →  František Wichterlová  ($144.54)   │
│  🌆  Top Revenue City     →  Prague                ($273.24)    │
│  🇺🇸  Most Invoices       →  USA                   (131 invoices)│
│  🎸  Top Rock Artist      →  AC/DC                 (18 tracks)  │
│  🌍  Universal Genre      →  Rock (dominant in 20+ countries)   │
│  ⏱️  Longest Track        →  How Many More Times   (711,836 ms) │
└─────────────────────────────────────────────────────────────────┘
```



## 🛠️ SQL Techniques Demonstrated

|Technique                             |Used In        |
|--------------------------------------|---------------|
|`GROUP BY` + `COUNT` / `SUM`          |Easy Q1–Q5     |
|Multi-table `JOIN` chains (4–5 tables)|Moderate Q1–Q3 |
|Correlated Subquery in `WHERE`        |Moderate Q3    |
|`WITH` CTE (Common Table Expression)  |Advanced Q1–Q3 |
|`ROW_NUMBER() OVER (PARTITION BY ...)`|Advanced Q2–Q3 |
|`SELECT DISTINCT` for deduplication   |Moderate Q1    |
|`ROUND()` for financial precision     |Advanced Q1, Q3|



## 🚀 How to Run

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/music-store-sql-analysis.git

# 2. Import the database (PostgreSQL)
psql -U postgres -f music_store_db.sql

# 3. Run any query file
psql -U postgres -d music_store -f queries/advanced_q3.sql
```

> Compatible with **PostgreSQL 13+**. Schema and data dump included in `/database`.



## 📁 Project Structure

```
music-store-sql-analysis/
│
├── 📂 database/
│   └── music_store_db.sql          # Full schema + seed data
│
├── 📂 queries/
│   ├── easy/
│   │   ├── q1_senior_employee.sql
│   │   ├── q2_top_countries.sql
│   │   ├── q3_top_invoices.sql
│   │   ├── q4_best_city.sql
│   │   └── q5_best_customer.sql
│   ├── moderate/
│   │   ├── q1_rock_listeners.sql
│   │   ├── q2_top_rock_bands.sql
│   │   └── q3_long_tracks.sql
│   └── advanced/
│       ├── q1_customer_artist_spend.sql
│       ├── q2_genre_by_country.sql
│       └── q3_top_customer_per_country.sql
│
├── 📂 results/                     # Query output screenshots
├── 📄 schema_diagram.png           # ERD / database schema
└── 📄 README.md
```

## 📊 Overall Business Insights

### 🌍 1. USA Dominates Sales Volume — But Prague Wins Revenue Density

The **USA** leads with **131 invoices**, making it the largest customer base by transaction count. However, **Prague** generates the **highest cumulative revenue ($273.24)** among all cities — meaning Prague customers spend *more per visit*. This split between volume and value is a critical business distinction: the USA needs retention strategies, while Prague deserves premium event investment like the proposed Music Festival.



### 👑 2. A Small Group of Customers Drive Disproportionate Revenue

The top customer — **František Wichterlová (Czech Republic)** — spent **$144.54**, nearly **3× the average customer spend**. This pattern holds across countries: in every single nation, one customer significantly outspends the rest. This signals a classic **Pareto distribution (80/20 rule)** — a small VIP tier is likely responsible for a large share of total revenue. A loyalty rewards program targeting these top spenders could yield outsized returns.



### 🎸 3. Rock is the Global King — But Niche Markets Exist

**Rock dominates purchases in 20+ countries** across all continents, making it the safest genre for global marketing campaigns. However, **Norway bucks the trend** with **Metal as its #1 genre** — proof that blanket global campaigns miss local nuance. A country-level genre targeting strategy (like the one this analysis enables) would outperform one-size-fits-all promotions.



### 🎤 4. AC/DC is the Store’s Most Commercially Powerful Artist

With **18 Rock tracks** in the catalog and the **highest customer spend tied to a single artist**, AC/DC is the store’s MVP artist. **Steve Murray alone spent $17.82 on AC/DC music**. This makes AC/DC an ideal anchor for themed playlists, featured collections, and artist-spotlight campaigns. The data also shows a sharp drop-off after the top 3 rock bands — the store’s revenue is heavily concentrated in a few high-performing artists.



### ⏱️ 5. Long-Form Tracks Are a Hidden Opportunity

The average track length sits around **393,599 ms (~6.5 minutes)**. Tracks exceeding this — like *“How Many More Times”* at **711,836 ms (~11.8 min)** — represent a dedicated audience segment that prefers extended, immersive listening experiences. Curating a **“Deep Cuts” or “Extended Plays” playlist** around these tracks could serve audiophile customers who are typically high-engagement, high-retention users.



### 💼 6. The Employee Hierarchy is Shallow — Potential Bottleneck

The senior-most employee is **Andrew Adams (General Manager, Level L6)**. With only 6 levels visible in the hierarchy, the organization is relatively flat. This is useful context for customer support analysis — if all `support_rep_id` assignments trace back through a narrow management chain, any staffing gaps at the top would have outsized impact on customer service quality.



### 🛒 7. Invoice Value Ceiling Suggests a Pricing Opportunity

The **maximum single invoice is $23.76** — surprisingly low for a music store. The top 3 invoices cluster between $19–$24, suggesting customers rarely make large bulk purchases. This points to either a **catalog pricing issue** (tracks priced too low to incentivize bundle buys) or a **lack of album/bundle deals**. Introducing curated album bundles or discounted multi-track purchases could raise average order value significantly.



## 🎓 Key SQL Skills Strengthened

```
✦ Multi-table JOIN architecture         ✦ CTE design patterns
✦ Window functions (ROW_NUMBER, RANK)   ✦ Subqueries vs CTEs trade-offs  
✦ Aggregate functions with HAVING       ✦ ROUND() for financial data
✦ DISTINCT deduplication strategy       ✦ PARTITION BY for group ranking
✦ Schema reading & ERD navigation       ✦ Query optimization thinking
```



*Built with 🎵 and SQL by **Uma Maheshwari***

## 👩‍💻 About the Author

<div align="center">

**Uma Maheshwari**
*Data Analyst | SQL Enthusiast | Business Intelligence*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/yourprofile)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/yourusername)

</div>

-----

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=12&height=100&section=footer" width="100%"/>

*⭐ If this project helped you, consider giving it a star!*

</div>
