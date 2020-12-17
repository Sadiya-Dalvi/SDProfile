# Setting Up Bigquery

1. Followed the Google documentation to setup bigquery for my project_id:
https://cloud.google.com/bigquery/docs/bigquery-web-ui

# Bigquery Data Import from sql db

1. Loaded movielens data from mysql db to bigquery. First I exported the data to csv files and then imported on to bigquery into a table. Then transformed the data using bigquery denormalization. I followed the steps mentioned in the following link https://cloud.google.com/solutions/performing-etl-from-relational-database-into-bigquery. The following figure shows the imported tables movie, genre, ratings and movie-genre-relationship table. The CSV files were located on a google cloud storage. Please click on the figure to view it clearly.

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/movietable.png" alt="Data Import from csv" width="700" height="300" >
</kbd>

Reference Link = https://cloud.google.com/solutions/performing-etl-from-relational-database-into-bigquery

wrote the following query for table denormalization:

`SELECT a.movieId as movieId,
       a.name as name, 
       a.releaseyear as releaseyear,
       b.rating as rating, b.userId as userid, TIMESTAMP_SECONDS(b.timestamp) as datetime,
       c.genre_name as genre
       from tidy-ivy-297317.movielens.movie as a, tidy-ivy-297317.movielens.ratings as b, tidy-ivy-297317.movielens.genre as c, tidy-ivy-297317.movielens.movie_rel as d
       where
       a.movieId = b.movieId
       and a.movieId = d.movieId`
       
 The following are the results:
 
 <kbd> 
 <img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/etl.png" alt="etl job to load data into movielens table" width="300" height="300">
 </kbd>
 
 
 <kbd>
 <img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/etl_transform_table_Rows.png" alt="etl job to load data into movielens table" width="700" height="300">
 </kbd>

 
       
# Data Import from public data set - NYC City Taxi Data

Loaded data under my project for the  NYC City Taxi DATASET. Please click on the figure to view it clearly.

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/publicdata.png" alt="Data Import from Public Dataset" width="700" height="300">
</kbd>

# Upload from Datastore

uploaded movielens data from datastore to bigquery using the export utility. Please click on the figure to view it clearly.

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/datastore upload.png" alt="Upload data from datastore" width="700" height="300">
</kbd>

# Query Run Comparison

##First Example

1.Imported the NYC Taxi dataset in Bigquery and wrote the following query:

Find the number of trips starting from 01-Jan-2015 to 31-Jan-2015 with less than 5 passengers in the taxi:

`SELECT
  COUNT(*) trips
FROM
  compact-cell-290609.NewYorkCityTaxi.tlc_green_trips_2015
WHERE
  pickup_datetime BETWEEN ("2015-01-01 00:00:00")
  AND ("2015-01-31 23:59:59")
  AND passenger_count < 5`
  
 Query results for the above query are as follows:

Query complete (2.4 sec elapsed, 293.5 MB processed)

| row  | trips    |
| ---  | -------- |
| 1    | 1421052  |

Optimized the query by using partioning and cluster tables. 

Wrote the query below to create the table

`create table
compact-cell-290609.NewYorkCityTaxi.tlc_green_trips_2015_clustered
partition by
Date(pickup_datetime) 
CLUSTER by PASSENGER_COUNT as
select * from compact-cell-290609.NewYorkCityTaxi.tlc_green_trips_2015`

Ran the following query again against the new table to see the results:

`SELECT
  COUNT(*) trips
FROM
  compact-cell-290609.NewYorkCityTaxi.tlc_green_trips_2015_clustered
WHERE
  pickup_datetime BETWEEN ("2015-01-01 00:00:00")
  AND ("2015-01-31 23:59:59")
  AND passenger_count < 5`

Query results for the above query are as follows:

Query complete (0.3 sec elapsed, 23 MB processed)

| row  | trips    |
| ---  | -------- |
| 1    | 1421052  |

Got some impressive results above.

##Example2

Ran the following query to  get the number of trips per year made by fhv taxis

`SELECT MAX(EXTRACT(YEAR from pickup_datetime)) as TripYear, count(1) as TripCount FROM compact-cell-290609.NewYorkCityTaxi.tlc_fhv_trips_2015
UNION ALL
SELECT MAX(EXTRACT(YEAR from pickup_datetime)) as TripYear, count(1) as TripCount FROM compact-cell-290609.NewYorkCityTaxi.tlc_fhv_trips_2016
UnION ALL
SELECT MAX(EXTRACT(YEAR from pickup_datetime)) as TripYear, count(1) as TripCount FROM compact-cell-290609.NewYorkCityTaxi.tlc_fhv_trips_2017
order by TripYear`

Query results

Query complete (1.9 sec elapsed, 2.9 GB processed)

|Row	|TripYear	|TripCount|	
|----- |-------------|---------| 
|1	|  2015       |63867609 |
|2	| 2016        |133285141|
|3	| 2017        |192092698|



Used wild cared (*) in the above query to get the result

`SELECT EXTRACT(YEAR from pickup_datetime) as TripYear, count(1) as TripCount FROM compact-cell-290609.NewYorkCityTaxi.tlc_fhv_trips_*
GROUP BY TripYear
ORDER BY TripYear`

Query results are as follows:

Query complete (1.5 sec elapsed, 2.9 GB processed)

|Row	|TripYear	|TripCount|	
|----- |-------------|---------| 
|1	|  2015       |63867609 |
|2	| 2016        |133285141|
|3	| 2017        |192092698|

Optimization was observed.
