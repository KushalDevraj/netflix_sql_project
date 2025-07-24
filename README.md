# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/KushalDevraj/netflix_sql_project/blob/main/Netfliximage.png)

## Objective
--Netflix Project 

drop table if exists netflix

create table netflix(
show_id varchar(6),
type varchar(10),
title varchar(150),
director varchar(208),
casts varchar(1000),
country varchar(150),
date_added varchar(50),
release_year int,
rating varchar(10),
duration varchar(15),
listed_in varchar(100),
description varchar(250)
);

select * from netflix

select count(show_id) from netflix

--14 Business Problems & Solutions

--1. Count the number of Movies vs Tv Shows
 select type,count(show_id) as count_number
 from netflix
 group by type;
 
--2. Find the most common rating for movies and tv shows
select * from(select type,rating,count(rating),
rank() over(partition by type order by count(*) desc) as t1
from netflix
group by 1,2) as ranked
where t1 = 1;

--3. List all movies released in specific year(e.g. 2020)
--filter 2020
--year 2020
select *  from netflix 
where type='Movie' 
and release_year = 2020;

--4. Find the top 5 countrires with the most content on netflix
select
unnest (string_to_array(country,',')) as new_country,count(show_id) as total_count
from netflix
group by 1
order by 2 desc
limit 5;

--5. Identify the longest movie
select * from netflix
where type = 'Movie' and duration = (select max(duration) from netflix);

--6. Find content added in last 5 years
select * from netflix 
where to_date(date_added,'Month DD,YYYY') >= current_date - interval '5 years';

--7. Find all the Movies/Tv Shows by director Rajiv Chilaka
select * from netflix
where director ilike '%Rajiv Chilaka%';

--8. List all tv shows with more than 5 seasons
select *
from netflix
where
type='TV Show'
and
where split_part(duration,' ',1)::numeric > 5 seasons;

--9. Count the number of content items in each genre 
select 
unnest(string_to_array(listed_in,',')),
count(show_id) as total_contents
from netflix 
group by 1

--10. Find each year and the average number of contents release by india on netflix 
--return the top 5 years with highest average content release:
select extract(year from to_date(date_added,'Month DD, YYYY')) as date_da,count(*), 
ROUND(
count(*)::numeric/(select count(*) from netflix where country='India')::numeric * 100) as avg_total
from netflix 
where country='India'
group by 1;

--11. List all the movies that are documentries
select * from netflix
where listed_in ilike '%documentaries%';

--12. Find all content without a director 
select * from netflix 
where director is null;

--13. Find how many movies actor salman khan appeared in last 10 years
select * from netflix 
where casts ilike '%salman khan%'
and 
release_year > extract(year from current_date) - 10;

--14. Find top 10 actors who appeared in he highest number of movies in India.
select 
unnest(string_to_array(casts,',')) as actors,count(*)
from netflix
where country ilike '%India%'
and type = 'Movie'
group by 1
order by 2 desc
limit 10

--15.Categorize the content based on the persence of the keywords 'kill' and 'voilence'
--in the description field.Label content containing these keywords as 'Bad' and all other 
--content as 'Good'.Count  how many items fall into each category
with new_table as 
(
select
case 
when description ilike '%kill%' or 
description ilike '%voilence%' then 'Bad_Content'
else 'Good_Content'
end as category,*
from netflix
) select category,count(*) as total_content
from new_table 
group by 1
