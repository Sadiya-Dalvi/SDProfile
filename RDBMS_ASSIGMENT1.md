
## Data Engineering - Module 1- Assigment1 - RDBMS and GCP Datastore


## Setup Instructions


1.Create a MYSQL instance on GCP

2.Connect to MySQL  on GCP using cloud shll

    ```gcloud sql connect movielens --user=root
     # enter password when asked to```


3. Inside shell, connect to your HR_ database to complete the exercises 
  ```# enter your database
  mysql> USE movielens
  ```

4. Check to see the tables are there

  ```sh
  mysql> SHOW TABLES;
  #initially there would be no tables in your db  
  ```
5.Before creating any tables download the movielens dataset  from http://files.grouplens.org/datasets/movielens/ml-latest.zip.
6. This dataset will have the following csv files:
	ratings.csv
	tags.csv
	movies.csv
	links.csv
	genome-scores.csv
	genome-tags.csv
7.Understand the dataset by going through the readme.txt file in ml-latest.zip
8.For the assignment queries, analyse which fields in which tables are important. 
9.Combine/transform the data from various tables and create csv as below:
 - movies-transform.csv (movieId, name, releaseyear)
 - movie-genre-rel.csv  (movieId, genre_id)
 - genre.csv (genre_id, genre)
 - ratings.csv (userId,	movieId,rating,	timestamp)
10, The timestamp in the ratings table is the unix_timestamp.This must be converted.
11. Upload the data to google cloud storage under the same project as your mysql db
12. Write queries as below to create table

	``` 
  CREATE TABLE movies (
	    movieId INTEGER PRIMARY KEY,
	    title TEXT NOT NULL,
	    releaseyear integer NOT NULL'''
	  );
    
    

	CREATE TABLE genre (
	   genreid INTEGER PRIMARY KEY,
	   movieId INTEGER,
	   genre TEXT ,
	   FOREIGN KEY(movieId) REFERENCES movies(movieId)
	    );```
	   
	   CREATE TABLE ratings(
	         userId INTEGER,
	         movieId INTEGER,
	        rating REAL,
	        timestamp TIMESTAMP,
	        FOREIGN KEY(movieId) REFERENCES movies(movieId),
	        PRIMARY KEY (userId, movieId)```
	    	
	    	
	    	 CREATE TABLE tags(
	         userId INTEGER,
	         movieId INTEGER,
	        tags text,
	        timestamp TIMESTAMP,
	        FOREIGN KEY(movieId) REFERENCES movies(movieId),
		PRIMARY KEY (userId, movieId)```

4. Insert the data in tables using the cloud storage console.

5. query the tables for the following:
```
a. Userwise, number of movies in a week

mysql> select userid, count(movieid) from ratings
    ->  where
    ->         datetime >= date_add('2017-01-01', interval  -WEEKDAY('2017-01-01')-1 day) and
    ->         datetime <= date_add(date_add('2017-01-01', interval  -WEEKDAY('2017-01-01')-1 day), interval 6 day)
    ->         group by userid;
    
+--------+----------------+
| userid | count(movieid) |
+--------+----------------+
|    321 |              2 |
|   1035 |              5 |
|   1383 |              2 |
|   1507 |              8 |
|   1880 |              3 |
|   1933 |              3 |
|   2025 |              2 |
|   2205 |              1 |
|   2242 |              1 |
|   2419 |              1 |
|   2745 |              3 |
|   2768 |             13 |
|   2867 |             10 |
|   3010 |              4 |
| 281975 |              2 |
| 282119 |              4 |
| 282203 |              2 |
| 282374 |              2 |
| 282405 |             24 |
| 282421 |              4 |
| 283117 |              1 |
+--------+----------------+
1346 rows in set (0.42 sec)



b. Number of movies per genre

    mysql>  SELECT  count(a.movieId), c.genre_name  from  movies a, movie_genre b, genre c
    -> where a.movieId = b.movieId
    -> AND b.genre_id = c.genre_id
    -> group by c.genre_id;
+------------------+--------------+
| count(a.movieId) | genre_name   |
+------------------+--------------+
      |       6821 | Action
   |          3863 | Adventure
   |          2590 | Animation
    |         2673 | Children
      |      15198 | Comedy
       |      4808 | Crime
    |         7815 | Thriller
     |        7071 | Romance
     |        2502 | Fantasy
 |            4884 | Documentary
       |     22896 | Drama
   |           333 | Film-Noir
      |       5275 | Horror
     |        1030 | Musical
     |        2624 | Mystery
      |       3272 | Sci-Fi
         |    1715 | War
     |        1314 | Western
        |     4223 | Null
+------------------+--------------+
19 rows in set (0.47 sec)
mysql> end

C. Number of viewers per movie genre - Wrote a stored procedure. The query inside the SP is listed below:

 
Call getUsersGenre();
    
SELECT  count(a.userId) as "Count of Users", c.genre_name  as "Genre Name"
from  genre c, movie_genre b, ratings a
where
b.movieId = a.movieId
AND b.genre_id = c.genre_id
group by c.genre_id
order by count(a.userId) desc;

 +----------------+--------------+
| Count of Users | Genre Name   |
+----------------+--------------+
       |   38876 | Drama
      |    30928 | Comedy
      |    26327 | Action
    |      24361 | Thriller
   |       19893 | Adventure
     |     16277 | Romance
      |    15418 | Sci-Fi
       |   14831 | Crime
     |      9535 | Fantasy
      |     7043 | Horror
    |       6927 | Children
     |      6762 | Mystery
   |        5311 | Animation
         |  4432 | War
     |      3178 | Musical
     |      1638 | Western
 |           971 | Documentary
   |         894 | Film-Noir
        |     52 | Null
+----------------+--------------+
19 rows in set (0.65 sec)

D. Time interval when the number of movies streamed is maximum - Wrote a stored procedure. The query inside the SP is listed below:

Call maxMovieWeek()
#Created a new table ratingsweek and inserted rows as below:

insert into ratingsWeek
        select count(rating), concat(year(datetime),week(datetime))
         from ratings
         group by concat(year(datetime),week(datetime));

select countWeek as "Count of Movies Watched", yearWeek as "Year and Week"
from ratingsWeek
order by countWeek
desc limit 10;


mysql> Call maxMovieWeek();
+-------------------------+---------------+
| Count of Movies Watched | Year and Week |
+-------------------------+---------------+
|                  248791 |        200047 |
|                  208458 |        199950 |
|                  187658 |        200512 |
|                  108269 |        200843 |
|                  101968 |        199940 |
|                   96333 |        201544 |
|                   93650 |        200513 |
|                   93548 |        200031 |
|                   82529 |        201533 |
|                   80380 |        199939 |
+-------------------------+---------------+
10 rows in set (0.20 sec)
Query OK, 0 rows affected (0.20 sec)
 
E. Number of movies per week group by genre sorted by number of movies - Wrote a stored procedure. The query inside the SP is listed below:

Call  getUsersGenreWeek(inputdate);

 SELECT  count(a.userId) as "Count of Users", c.genre_name  as "Genre Name"
from  genre c, movie_genre b, ratings a
where
a.datetime >= date_add(inputDate, interval  -WEEKDAY(inputDate)-1 day) and
a.datetime <= date_add(date_add(inputDate, interval  -WEEKDAY(inputDate)-1 day), interval 6 day) and
a.movieId = b.movieId
AND b.genre_id = c.genre_id
group by c.genre_id
order by count(a.userId) desc;

 mysql> call getUsersGenreWeek('2016-05-05');
+----------------+--------------+
| Count of Users | Genre Name   |
+----------------+--------------+
       |   17028 | Drama
      |    14213 | Action
      |    12651 | Comedy
   |       11481 | Adventure
    |      10763 | Thriller
      |     9108 | Sci-Fi
       |    6480 | Crime
     |      6364 | Romance
     |      5201 | Fantasy
    |       3311 | Children
   |        3183 | Animation
     |      3144 | Mystery
      |     2660 | Horror
         |  2020 | War
     |      1145 | Musical
     |           706 | Documentary
     |       621 | Western
   |         275 | Film-Noir
        |     87 | Null
+----------------+--------------+
19 rows in set (0.49 sec)
     
    
F. Least watched movies per week - Wrote a stored procedure. The query inside the SP is listed below:

Call leastMovieWatchedWeek('2016-05-05');
DECLARE firstDay datetime;
DECLARE lastDay  datetime;

    set firstDay = date_add(inputDate, interval  -WEEKDAY(inputDate)-1 day);
    set lastDay  = date_add(date_add(inputDate, interval  -WEEKDAY(inputDate)-1 day), interval 6 day);
 

    insert into tempMovieTable (movieId, countMovie)
    select              
      movieId, count(movieId)
       from ratings 
        where
        datetime >= firstDay and
        datetime < lastDay
    group by movieId
    order by count(movieId) asc
    limit 5;

    select movies.title, movies.releaseyear, tempMovieTable.countMovie 
    from tempMovieTable, movies
    where movies.movieId = tempMovieTable.movieId;
 delete from tempMovieTable where movieId > 0;
 
 mysql> Call leastMovieWatchedWeek('2016-05-05');
+-----------------------------+-------------+------------+
| title                       | releaseyear | countMovie |
+-----------------------------+-------------+------------+
| Lizzie McGuire Movie, The   |        2003 | 1          |
| Camp Rock 2: The Final Jam  |        2010 | 1          |
| Snitch                      |        2013 | 1          |
| Woody Allen: A Documentary  |        2012 | 1          |
| Life Is Ruff                |        2005 | 1          |
+-----------------------------+-------------+------------+
5 rows in set (0.26 sec)
Query OK, 5 rows affected (0.26 sec)


#8. Highest and lowest watched movies within a date range

Call countMoviesDateRange('2018-05-05','2018-05-10');
     insert into tempMovieTable (movieId, countMovie)
    select              
   movieId, count(movieId)
               from ratings 
               where
        datetime >= firstDay and
        datetime <= lastDay
               group by movieId
    order by count(movieId) desc
    limit 5;   
   
    insert into tempMovieTable (movieId, countMovie)
    select              
   movieId, count(movieId)
              from ratings 
               where
        datetime >= firstDay and
        datetime <= lastDay
               group by movieId
    order by count(movieId) asc
    limit 5;
    select movies.title, movies.releaseyear, tempMovieTable.countMovie 
    from tempMovieTable, movies
    where movies.movieId = tempMovieTable.movieId
    order by tempMovieTable.countMovie desc;
    delete from tempMovieTable where movieId > 0;

mysql> call countMoviesDateRange('2013-06-10', '2013-06-15');
+----------------------------+-------------+------------+
| title                      | releaseyear | countMovie |
+----------------------------+-------------+------------+
| Shawshank Redemption, The  |        1994 | 41         |
| Dark Knight, The           |        2008 | 34         |
| Matrix, The                |        1999 | 32         |
| Fight Club                 |        1999 | 30         |
| Inception                  |        2010 | 27         |
| Enter the Dragon           |        1973 | 1          |
| Perfect Murder, A          |        1998 | 1          |
| Session 9                  |        2001 | 1          |
| EuroTrip                   |        2004 | 1          |
| Gigli                      ---+-------------+------------+

10 rows in set (0.25 sec)
Query OK, 10 rows affected (0.26 sec)

#9. Top five and bottom five movie rating within a date range"

Call ratingsMoviesDateRange('2013-06-10', '2013-06-15');
  insert into tempMovieTable (movieId, ratingMovie)
    select      
    movieId, sum(rating)/count(movieId)
               from ratings 
               where
        datetime >= firstDay and
        datetime <= lastDay
               group by movieId
    order by sum(rating)/count(movieId) desc
    limit 5;   
    insert into tempMovieTable (movieId, ratingMovie)
    select              
                  movieId, sum(rating)/count(movieId)
               from ratings 
               where
        datetime >= firstDay and
        datetime <= lastDay
               group by movieId
    order by sum(rating)/count(movieId) asc
    limit 5;

    select movies.title, movies.releaseyear, tempMovieTable.ratingMovie as averageRating
    from tempMovieTable, movies
    where movies.movieId = tempMovieTable.movieId
    order by tempMovieTable.ratingMovie desc;
  delete from tempMovieTable where movieId > 0;


mysql> Call ratingsMoviesDateRange('2013-06-10', '2013-06-15');
+----------------------------------+-------------+---------------+
| title                            | releaseyear | averageRating |
+----------------------------------+-------------+---------------+
| Dirty Dancing                    |        1987 |             5 |
| Mermaids                         |        1990 |             5 |
| Wild Strawberries                |           0 |             5 |
| Away We Go                       |        2009 |             5 |
| Courageous                       |        2011 |             5 |
| Bamboozled                       |        2000 |             0 |
| Sex and Lucia                    |           0 |             0 |
| Drumline                         |        2002 |             0 |
| Happiness of the Katakuris, The  |           0 |             0 |
| Awake                            |        2007 |             0 |
+----------------------------------+-------------+---------------+
10 rows in set (1.36 sec)
Query OK, 10 rows affected (1.36 sec)





from google.cloud import datastore
# Create, populate and persist an entity with keyID=1234
client = datastore.Client()
key = client.key('EntityKind', 1234)
entity = datastore.Entity(key=key)
entity.update({
    'foo': u'bar',
    'baz': 1337,
    'qux': False,
})
client.put(entity)
# Then get by key for this entity
result = client.get(key)
print(result)
https://pypi.org/project/google-cloud-datastore/
