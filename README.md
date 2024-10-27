# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/srinivas77777775/Netflix_SQL_PROJECT_/blob/main/Netflix-Logo.wine.png)

## Objectives

- Examine how different kinds of content are distributed (TV series vs. movies).
- Determine the most popular movie and TV show ratings.
- List and evaluate content according to countries, durations, and years of release.
- Examine and group content according to particular standards and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
-- Netflix project 
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id VARCHAR(6),
    type    VARCHAR(10),  	
    title	VARCHAR(150),
    director	VARCHAR(208),
    casts	VARCHAR(1000),
    country	VARCHAR(150),
    date_added	VARCHAR(50),
    release_year	INT,
    rating	VARCHAR(10),
    duration	VARCHAR(15),
    listed_in	VARCHAR(100),
    description VARCHAR(250)
);
```

## Business Problems and Solutions

### 1. count the number of Movies vs TV Shows

```sql
SELECT 
    type, count(*) as total_content 
FROM netflix 
GROUP BY type
```
### 2. Find the most common rating for movies and TV shows
```sql
SELECT
    type,
	rating
FROM

(
    SELECT
       type,
	   rating,
	   COUNT(*),
	   RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) as ranking
   FROM netflix
   GROUP BY 1, 2
) as t1
WHERE
    RANKING = 1
```

### 3. List all movies released in a specific year (e.g., 2020)
```sql
SELECT * FROM netflix
WHERE 
    type = 'Movie'
    AND
    release_year = 2020
```
### 4. Find the top 5 countries with the most content on Netflix
```sql
SELECT
     UNNEST(STRING_TO_ARRAY(country, ',')) as new_country,
	 COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```
### 5. Identify the longest movie?
```sql
SELECT * FROM netflix
WHERE
    type = 'Movie'
	AND
	duration = (SELECT MAX(duration) FROM netflix)
```
### 6. Find content added in the last 5 years
```sql
SELECT
    *	
FROM netflix
WHERE 
    TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years'
```
### 7. Find all the movies/TV shows by director 'Rajiv Chilaka'
```sql
SELECT * FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%'
```
### 8. List all TV shows with more than 5 seasons
```sql
SELECT * FROM netflix
WHERE 
    type = 'TV Show'
	AND
	SPLIT_PART(duration, ' ', 1)::numeric > 5
```
### 9. Count the number of content items in each genre
```sql
SELECT
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
	COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
```
### 10. Find each year and the average numbers of content release by India on netflix. return top 5 year with highest average content release.
```sql
SELECT
    EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) as year,
	COUNT(*) as yearly_content,
	ROUND(
    COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100
	,2)as avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1
```
### 11. List all movies that are documentaries
```sql
SELECT * FROM netflix
WHERE
   listed_in ILIKE '%documentaries%'
```
### 12. Find all content without a director
```sql
SELECT * FROM netflix
WHERE
   director IS NULL
```
### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
```sql
SELECT * FROM netflix
WHERE
   casts ILIKE '%Salman Khan%'
   AND
   release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10
```
### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.
```sql
SELECT 
UNNEST(STRING_TO_ARRAY(casts, ',')) as actors,
COUNT(*) as total_content
FROM netflix
WHERE country ILIKE '%india'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```
### 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
```sql
WITH new_table 
AS
(
SELECT
*,
  CASE
  WHEN
     description ILIKE '%Kill%' OR
	 description ILIKE '%violence%' THEN 'Bad_Content'
	 ELSE 'Good Content'
  END category
FROM netflix
)
SELECT
     category,
	 COUNT(*) as total_content
FROM new_table
GROUP BY 1
```

## Results and Conclusion

- **Content Distribution:** The dataset includes a wide variety of TV series and films with different genres and ratings.
- **Common Ratings:** Knowledge of the most popular ratings helps determine who the content is intended for.
- **Geographical Insights:** Regional content distribution is highlighted by the top countries and the average content releases by India.
- **Content Categorization:** Sorting content according to particular keywords aids in comprehending the type of content that is accessible on Netflix.

This research offers a thorough look into Netflix's content and can assist in guiding decision-making and content strategy.



This project, which is a component of my portfolio, demonstrates the SQL abilities necessary for positions as data analysts. Please contact if you would like to work together or if you have any questions or comments!


