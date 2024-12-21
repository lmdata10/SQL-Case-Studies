# IMDb SQL Case Study

This repository contains a SQL case study based on the IMDb Top 1000 Movies and TV Shows dataset. The case study focuses on creating a SQL table, inserting data, and solving a variety of analytical questions using SQL queries.

---

## Dataset

The dataset is sourced from [Kaggle: IMDb Dataset of Top 1000 Movies and TV Shows](https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows).

Key columns include:

- **Poster_Link**: Link to the movie poster  
- **Series_Title**: Title of the movie or show  
- **Released_Year**: Release year  
- **Certificate**: Certification (e.g., A, UA)  
- **Runtime**: Duration of the movie  
- **Genre**: Genre of the movie  
- **IMDB_Rating**: IMDb rating  
- **Director**: Director's name  
- **Stars**: Main cast (Star1, Star2, Star3, Star4)  
- **Gross**: Total box office gross  

---

## SQL Table Schema

Below is the SQL script to create the `imdb_top_movies` table:

```sql
DROP TABLE IF EXISTS imdb_top_movies;
CREATE TABLE IF NOT EXISTS imdb_top_movies
(
	Poster_Link		varchar(4000),
	Series_Title	varchar(1000),
	Released_Year	varchar(10),
	Certificate		varchar(10),
	Runtime			varchar(20),
	Genre			varchar(50),
	IMDB_Rating		decimal,
	Overview		varchar(4000),
	Meta_score		int,
	Director		varchar(200),
	Star1			varchar(200),
	Star2			varchar(200),
	Star3			varchar(200),
	Star4			varchar(200),
	No_of_Votes		bigint,
	Gross			money
);

SELECT * FROM imdb_top_movies;
```

## Questions:

**1) Fetch all data from imdb table**

```sql
SELECT *
FROM imdb_top_movies
```

**2) Fetch only the name and release year for all movies.**

```sql
SELECT 
	series_title
	, released_year 
FROM imdb_top_movies;
```

**3) Fetch the name, release year and imdb rating of movies which are UA certified.**

```sql
SELECT series_title
	, released_year
	, imdb_rating
FROM imdb_top_movies
WHERE certificate = 'UA';
```

**4) Fetch the name and genre of movies which are UA certified and have a Imdb rating of over 8.**

```sql
SELECT series_title
	, genre
FROM imdb_top_movies
WHERE certificate = 'UA' AND imdb_rating > 8;
```

**5) Find out how many movies are of Drama genre.**

```sql
SELECT COUNT(*)
FROM imdb_top_movies
WHERE genre LIKE '%Drama%';
```

**6) How many movies are directed by "Quentin Tarantino", "Steven Spielberg", "Christopher Nolan" and "Rajkumar Hirani".**

```sql
SELECT COUNT(*)
FROM imdb_top_movies
WHERE director IN ('Quentin Tarantino', 'Steven Spielberg', 'Christopher Nolan', 'Rajkumar Hirani');
```

**7) What is the highest imdb rating given so far?**

```sql
SELECT MAX(imdb_rating) AS max_rating
FROM imdb_top_movies;
```

**8) What is the highest and lowest imdb rating given so far?**

```sql
SELECT MAX(imdb_rating) AS highest_rating, MIN(imdb_rating) AS lowest_rating
FROM imdb_top_movies;
```

**8a) Solve the above problem but display the results in different rows.**

```sql
SELECT MAX(imdb_rating) AS highest_rating FROM imdb_top_movies
UNION
SELECT MIN(imdb_rating) AS lowest_rating FROM imdb_top_movies;
```

**8b) Solve the above problem but display the results in different rows along with an extra column which indicates the value as lowest and highest.**

```sql
SELECT MAX(imdb_rating) AS imdb_rating,'HIGHEST' AS type
FROM imdb_top_movies
UNION
SELECT MIN(imdb_rating) AS imdb_rating,'LOWEST' AS type
FROM imdb_top_movies;
```

**9) Find out the total business done by movies staring "Aamir Khan".**

```sql
SELECT SUM(gross) AS total_business
FROM imdb_top_movies
-- WHERE star1 = 'Aamir Khan' OR star2 = 'Aamir Khan' OR star3 = 'Aamir Khan'
WHERE 'Aamir Khan' IN (star1, star2, star3, star4);
```


**10) Find out the average imdb rating of movies which are neither directed by "Quentin Tarantino", "Steven Spielberg", "Christopher Nolan" and are not acted by any of these stars "Christian Bale", "Liam Neeson", "Heath Ledger", "Leonardo DiCaprio", "Anne Hathaway".**

```sql
SELECT AVG(imdb_rating)
FROM imdb_top_movies
WHERE 
	director NOT IN ('Quentin Tarantino', 'Steven Spielberg', 'Christopher Nolan')
AND 
	(
	star1 NOT IN ('Christian Bale', 'Liam Neeson', 'Heath Ledger', 'Leonardo DiCaprio', 'Anne Hathaway')
	AND star2 NOT IN ('Christian Bale', 'Liam Neeson', 'Heath Ledger', 'Leonardo DiCaprio', 'Anne Hathaway')
	AND star3 NOT IN ('Christian Bale', 'Liam Neeson', 'Heath Ledger', 'Leonardo DiCaprio', 'Anne Hathaway')
	AND star4 NOT IN ('Christian Bale', 'Liam Neeson', 'Heath Ledger', 'Leonardo DiCaprio', 'Anne Hathaway')
	);
```

**11) Mention the movies involving both "Steven Spielberg" and "Tom Cruise".**

```sql
SELECT series_title AS movies
FROM imdb_top_movies
WHERE director = 'Steven Spielberg' AND 'Tom Cruise' IN (star1, star2, star3, star4);
```

**12) Display the movie name and watch time (in both mins and hours) which have over 9 imdb rating.**

```sql
SELECT 
    series_title AS movies, 
    runtime AS runtime_minutes, 
	-- ROUND(CAST(SPLIT_PART(runtime, ' ', 1) AS DECIMAL) / 60, 2) AS runtime_hours
	-- ROUND(CAST(REPLACE(runtime, ' min', '') AS DECIMAL)/60,2) AS runtime_hours
	ROUND(REPLACE(runtime, ' min', '') :: DECIMAL/60,2)
	FROM imdb_top_movies
WHERE imdb_rating > 9;
```

**13) What is the average imdb rating of movies which are released in the last 10 years and have less than 2 hrs of runtime.**

```sql
SELECT 
    AVG(imdb_rating) AS avg_imdb_rating
FROM imdb_top_movies
WHERE 
    released_year >= '(EXTRACT(YEAR FROM CURRENT_DATE) - 10)'
	-- released_year >= (EXTRACT(YEAR FROM CURRENT_DATE) - 10) :: VARCHAR
    AND REPLACE(runtime, ' min', '') :: DECIMAL < 120;
	-- AND released_year <> 'PG'
```


**14) Identify the Batman movie which is not directed by "Christopher Nolan".**

```sql
SELECT series_title AS movies
FROM imdb_top_movies
WHERE UPPER(series_title) LIKE '%BATMAN%' 
	AND director != 'Christopher Nolan'
```

**15) Display all the A and UA certified movies which are either directed by "Steven Spielberg", "Christopher Nolan"  or which are directed by other directors but have a rating of over 8.**

```sql
SELECT series_title AS movies
FROM imdb_top_movies
WHERE certificate IN ('A', 'UA') 
AND (director IN ('Steven Spielberg', 'Christopher Nolan')
OR (director NOT IN ('Steven Spielberg', 'Christopher Nolan') AND imdb_rating > 8));
```

**16) What are the different certificates given to movies?**

```sql
SELECT DISTINCT certificate
FROM imdb_top_movies
WHERE certificate IS NOT NULL;
```

**17) Display all the movies acted by Tom Cruise in the order of their release. Consider only movies which have a meta score.**

```sql
SELECT series_title AS movies
FROM imdb_top_movies
WHERE 'Tom Cruise' IN (star1, star2, star3, star4) AND meta_score IS NOT NULL
ORDER BY released_year
```

**18) Segregate all the Drama and Comedy movies released in the last 10 years as per their runtime. Movies shorter than 1 hour should be termed as short film. Movies longer than 2 hrs should be termed as longer movies. All others can be termed as Good watch time.**

```sql
SELECT series_title
	,ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) AS runtime_hours
	,CASE
		WHEN ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) < 1 THEN 'Short Movie'
		WHEN ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) > 2 THEN 'Longer Movie'
		ELSE 'Good Watch Time'
	END AS rumtime_category
FROM imdb_top_movies
WHERE LOWER(genre) LIKE '%drama%' OR LOWER(genre) LIKE '%comedy%'
```


**19) Write a query to display the "Christian Bale" movies which released in odd year and even year. Sort the data as per Odd year at the top.**

```sql
SELECT series_title AS movies
	,released_year
	,CASE
		WHEN released_year :: INT % 2 = 0 THEN 'Even Year'
		ELSE 'Odd Year'
	END AS year_category
FROM imdb_top_movies
WHERE 'Christian Bale' IN (director,star1, star2, star3, star3)
ORDER BY year_category DESC;
```

**20) Re-write problem #18 without using case statement.**

```sql
SELECT series_title
	,ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) AS runtime_hours
	,'Short Movie' AS rumtime_category
FROM imdb_top_movies
WHERE (LOWER(genre) LIKE '%drama%' OR LOWER(genre) LIKE '%comedy%')
	AND ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) < 1
UNION ALL
SELECT series_title
	,ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) AS runtime_hours
	,'Longer Movie' AS rumtime_category
FROM imdb_top_movies
WHERE (LOWER(genre) LIKE '%drama%' OR LOWER(genre) LIKE '%comedy%')
	AND ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) BETWEEN 1 AND 2
UNION ALL
SELECT series_title
	,ROUND(REPLACE(runtime, ' min', '')::DECIMAL/60,2) AS runtime_hours
	,'Good Watch Time' AS rumtime_category
FROM imdb_top_movies
WHERE (LOWER(genre) LIKE '%drama%' OR LOWER(genre) LIKE '%comedy%')
```


> **Extra Questions:**

**Split the value '1234_1234' into 2 seperate columns having 1234 each.**

```sql
SELECT SPLIT_PART('1234_1234', '_',1) as Column1,
SPLIT_PART('1234_1234', '_',2) as Column2;

SELECT SUBSTRING('1234_1234',1, POSITION('_' IN '1234_1234')-1) AS Column1
	, SUBSTRING('1234_1234',POSITION('_' IN '1234_1234')+1) AS Column2
```

**We see a string value 'PG' in released_year and we hardcoaded it, can we make a query dynamic to identify string value incase if we have multiple string values in-order to ignore those string values. (Write a query to identify non numeric values in a column.)**

```sql
-- REGEX Regular Expression
SELECT * FROM imdb_top_movies
WHERE released_year !~ '[0-9]';
```
