# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/KushalDevraj/netflix_sql_project/blob/main/Netfliximage.png)

## Objective
# 📺 Netflix Data Analysis using SQL

This project analyzes the Netflix dataset using SQL to solve 15 real-world business problems. Each problem is answered with a relevant SQL query, making it a complete exploratory data analysis (EDA) using PostgreSQL.

---

## 📁 Dataset

- 📌 Source: [Kaggle - Netflix Shows Dataset](https://www.kaggle.com/shivamb/netflix-shows)
- 📄 Description: Contains information about TV Shows and Movies on Netflix, including title, cast, director, country, release year, rating, duration, genres, and description.

---

## ❓ Business Questions and Solutions

### 1️ Count the number of Movies vs TV Shows
**Problem:** How many Movies and TV Shows are there on Netflix?
```sql
SELECT type, COUNT(show_id) AS count_number
FROM netflix
GROUP BY type;
2️⃣ Most Common Rating for Movies and TV Shows
Problem: What is the most common rating given to Movies and TV Shows?

sql
Copy
Edit
SELECT * 
FROM (
  SELECT type, rating, COUNT(rating),
         RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS t1
  FROM netflix
  GROUP BY 1, 2
) AS ranked
WHERE t1 = 1;
3️⃣ Movies Released in 2020
Problem: List all the movies that were released in the year 2020.

sql
Copy
Edit
SELECT *  
FROM netflix 
WHERE type = 'Movie' 
  AND release_year = 2020;
4️⃣ Top 5 Countries by Content Count
Problem: Which 5 countries have the most content on Netflix?

sql
Copy
Edit
SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country,
       COUNT(show_id) AS total_count
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
5️⃣ Longest Movie
Problem: What is the longest movie available on Netflix?

sql
Copy
Edit
SELECT * 
FROM netflix
WHERE type = 'Movie' 
  AND duration = (SELECT MAX(duration) FROM netflix);
6️⃣ Content Added in the Last 5 Years
Problem: Which content was added to Netflix in the last 5 years?

sql
Copy
Edit
SELECT * 
FROM netflix 
WHERE TO_DATE(date_added, 'Month DD,YYYY') >= CURRENT_DATE - INTERVAL '5 years';
7️⃣ Content by Rajiv Chilaka
Problem: List all content (Movies or TV Shows) directed by Rajiv Chilaka.

sql
Copy
Edit
SELECT * 
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
8️⃣ TV Shows with More Than 5 Seasons
Problem: Which TV Shows have more than 5 seasons?

sql
Copy
Edit
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
9️⃣ Content Count by Genre
Problem: How many content items belong to each genre/category?

sql
Copy
Edit
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
       COUNT(show_id) AS total_contents
FROM netflix 
GROUP BY 1;
🔟 Average Content Released by India (Top 5 Years)
Problem: What are the top 5 years where India released the most content on Netflix?

sql
Copy
Edit
SELECT EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year_added,
       COUNT(*) AS total_count,
       ROUND(
         COUNT(*)::NUMERIC / 
         (SELECT COUNT(*) FROM netflix WHERE country = 'India')::NUMERIC * 100
       ) AS avg_total
FROM netflix 
WHERE country = 'India'
GROUP BY 1
ORDER BY avg_total DESC
LIMIT 5;
1️⃣1️⃣ Documentary Movies
Problem: List all the movies that are documentaries.

sql
Copy
Edit
SELECT * 
FROM netflix
WHERE listed_in ILIKE '%documentaries%';
1️⃣2️⃣ Content Without a Director
Problem: Which content does not have a director listed?

sql
Copy
Edit
SELECT * 
FROM netflix 
WHERE director IS NULL;
1️⃣3️⃣ Salman Khan Movies in the Last 10 Years
Problem: How many movies has Salman Khan appeared in during the last 10 years?

sql
Copy
Edit
SELECT * 
FROM netflix 
WHERE casts ILIKE '%salman khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
14 Top 10 Actors in Indian Movies
Problem: Who are the top 10 actors that appeared in the most Indian movies?

sql
Copy
Edit
SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actors,
       COUNT(*) AS appearance_count
FROM netflix
WHERE country ILIKE '%India%' 
  AND type = 'Movie'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
1️⃣5️⃣ Categorize Content as 'Good' or 'Bad' Based on Description
Problem: Categorize content as Bad if the description contains "kill" or "violence", otherwise Good.

sql
Copy
Edit
WITH new_table AS (
  SELECT CASE 
           WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' 
             THEN 'Bad_Content'
           ELSE 'Good_Content'
         END AS category,
         *
  FROM netflix
)
SELECT category, COUNT(*) AS total_content
FROM new_table 
GROUP BY 1;
