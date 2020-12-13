
# Using GCP Datastore

1. Create a firestore in datastore mode.

2. Create entities using the console

3. to load data into the entities use the following code:
follow the instructions given in the link https://cloud.google.com/datastore/docs/reference/libraries#command-line

from google.cloud import datastore
# Create, populate and persist an entity with keyID=1234
client = datastore.Client()
key = client.key('movies', 1234)
entity = datastore.Entity(key=key)
entity.update({
		"movieId" : 2,
		"title" : u'Jumanji',
		"releaseyear" : 1995,
		"rating" : 4,
		"datetime" : u'2018-03-26 21:51:45',
		"unixtimestamp" : 1522101105,
		"userId" : 14,
		"genre_id" : 2
})
client.put(entity)
# Then get by key for this entity
result = client.get(key)
print(result)


add multiple values

4. query the datastore

query = client.query(kind="movies")
query.add_filter("name", "=", Jumanji)
query.add_filter("releaseyear", "=", 2007)
