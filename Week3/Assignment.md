# Read a csv file from gcp cloud storage and view the contents using python. Had to import pandas and pyarrow package
```
import sys

import csv

import datetime

import pandas as pd

from google.cloud import storage

storage_client = storage.Client()

bucket = storage_client.get_bucket('movielensnosql1234567')

blob = bucket.get_blob('Module3/movies-ransform2.csv')

bt = blob.download_as_string()

from io import 

s = str(bt, "utf-8")

s = StringIO(s)

df = pd.read_csv(s)

print(df)
````


# result

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/csvfile.png" alt="Read Data from CSV File" width="700" height="300">
</kbd>

# Upload a parquet file on gcp cloud storage and the view contents using python.
```
import sys

import csv

import datetime

import pandas as pd

from google.cloud import storage

storage_client = storage.Client()

read_df = pd.read_parquet("gcs://movielensnosql1234567/Module3/userdata1.parquet", engine='pyarrow')

print(read_df)
```

# result

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/parquetfile.jpg" alt="Read Data from Parquet File" width="700" height="300">
</kbd>


# Run ELT processes to load data from source into data lake (google cloud stoarge). And run some queries on the data using spark. 
1) Created DataProc Cluster with anaconda and jupyter
	1 Master Node, 2 Worker Nodes
	
<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/cluster.png" alt="cluster" width="700" height="300">
</kbd>

2) Created Bucket for raw csv data (NYC taxi data)

3) SSH into the Master VM and Imported data from public source to Master VM using the following command:
	
 `wget https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2019-01.csv` 
		
4) Copied data from Master VM to Bucket (nyc-taxi-data-sadiya)using the following command:

`gsutil cp yellow_tripdata_2019-01.csv us-central1 gs://nyc-taxi-data-sadiya`

5) Opened the Jupyterlabs console and Read data to PySpark DataFrame

`nyctaxi_df = spark.read.csv("gs://nyc-taxi-data-sadiya", header=True, inferSchema=True)`

4) Queries

Query 1 - Average trip distance group by passenger count:

`nyctaxi_df.groupby('passenger_count').agg({'trip_distance': 'mean'}).show()`


|passenger_count|avg(trip_distance)|
|---------------|------------------|
|              1| 2.914812946997421|
|              6|2.9603002111340593|
|              3| 2.995860962968582|
|              5|2.9786438352819293|
|              9|2.9085714285714284|
|              4|  3.03757772596047|
|              8| 5.256999999999999|
|              7| 2.122702702702703|
|              2| 3.046500330346657|
|              0| 2.804823802554187|




Query 2 - Trips by passenger count ordered by passenger count in descending order


`nyctaxi_df.groupby('passenger_count').count().orderBy(nyctaxi_df.passenger_count.desc()).show()`


|passenger_count|   count|
|---------------|--------|
|              9|      84|
|              8|     130|
|              7|     148|
|              6|  958159|
|              5| 1597129|
|              4|  720480|
|              3| 1570747|
|              2| 5606232|
|              1|26407729|
|              0|  657274|



# Do some visualizations to show a simple analysis on the data.

## Visualisation and plotting with Matplotlib

`%matplotlib inline`

#creating a view to run visualizations

`nyctaxi_df.createOrReplaceTempView('trips_table')`


1.Plot a bar chart showing passenger count on x axis and  average trip distance on y axis

`trips3 = spark.sql("SELECT passenger_count, avg(trip_distance)  \
                   FROM trips_table \
                   GROUP BY passenger_count")
trips3.show()`



|passenger_count|avg(trip_distance)|
|---------------|------------------|
|              1|2.9148129469974213|
|              6| 2.960300211134059|
|              3|2.9958609629685813|
|              5|2.9786438352819293|
|              9|2.9085714285714284|
|              4|  3.03757772596047|
|              8| 5.256999999999999|
|              7| 2.122702702702703|
|              2| 3.046500330346657|
|              0|2.8048238025541865|


`plotTrip = trips3.toPandas()
plotTrip.plot(kind="bar", x="passenger_count", y="avg(trip_distance)")`

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/vis1.png" alt="Visualization1" width="700" height="300">
</kbd>

2.Plot a bar chart showing avg tip amount on y axis and passenger count on x axis

`trips3 = spark.sql("SELECT passenger_count, avg(tip_amount)  \
                   FROM trips_table \
                   GROUP BY passenger_count")
trips3.show()`


|passenger_count|   avg(tip_amount)|
|---------------|------------------|
|              1|2.1442412670175104|
|              6| 2.156313263247605|
|              3| 2.082967899986515|
|              5| 2.177697549791012|
|              9| 5.619523809523808|
|              4|1.9799634549189653|
|              8| 6.362538461538462|
|              7| 6.922364864864865|
|              2| 2.135891960946389|
|              0|2.0820742795242144|



`plotTrip = trips3.toPandas()
plotTrip.plot(kind="bar", x="passenger_count", y="avg(tip_amount)")`

<kbd>
<img src="https://github.com/Sadiya-Dalvi/SDProfile/blob/main/Images/vis2.png" alt="Visualization2" width="700" height="300">
</kbd>

# Transforming/Cleaning up the dataset 

1) Removing Duplicates

`nyctaxi_df.count()`

result

37518112

```
nyctaxi_df = nyctaxi_df.dropDuplicates()

nyctaxi_df.count()
```
result

37518066

2)Creating dataframe where fareamount is greater than zero

```
nyctaxi_df = nyctaxi_df.filter(nyctaxi_df.fare_amount > 0)

nyctaxi_df.count()
```

result

37450456



