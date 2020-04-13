# Big Data Analytics - MongoDB

## Exercise: Sharding in MongoDB
### What is sharding in mongoDB? 
Sharding is a way to distribute collection chunks across multiple clusters. These shards are handled by the Config servers using the chards metadata. 

Sharding is a method for distributing data across multiple machines. This means its horizontal scaling.

Horizontal Scaling involves dividing the system dataset and load over multiple servers.🚀🚀

### What are the different components required to implement sharding? 

* **shard**: Each shard contains a subset of the sharded data. Each shard can be deployed as a replica set.  
* **mongos**: The mongos acts as a query router, providing an interface between client applications and the sharded cluster.  
* **config servers**: Config servers store metadata and configuration settings for the cluster. As of MongoDB 3.4, config servers must be deployed as a replica set (CSRS).

### Explain architecture of sharding in mongoDB?
![sharding architecture](https://docs.mongodb.com/manual/_images/sharded-cluster-production-architecture.bakedsvg.svg)

## Exercise: MapReduce with  mongoDB (hashtag query) (5 points)
For this task you need to download twitter dataset from the link mentioned in 2) (https://github.com/ozlerhakan/mongodb-json-files/blob/master/datasets/tweets.zip). This time you have to answer query “what are the top 10 hashtags used in the given tweets”. To answer this you need to use MapReduce. You can look at the scheme of the collection using db.collection.findOne(). It will print one record with scheme information. Also you can use function like this.hasOwnProperty(‘field_name’) to check if a field exist in the record. (if the field does not exist you will get error.

### Provide implementation of map and reduce function

#### index.js
```js
function getHashtagAmounts(collection) {
	const tweetHashtags = collection.map((tweet) => {
		if (tweet.entities && tweet.entities.hashtags) return tweet.entities.hashtags.map((hashtag) => hashtag.text);
		return [];
	});

	const hashtags = tweetHashtags.reduce((acc, val) => acc.concat(val), []);

	const hashtagsAmounts = hashtags.reduce((obj, hashtag) => {
		if (obj[hashtag]) obj[hashtag] += 1;
		else obj[hashtag] = 1;

		return obj;
	}, {});

	return hashtagsAmounts;
}
```

### Provide execution command for running MapReduce

#### mongo shell
```js
load('index.js');
```

#### mongo shell
```js
getHashtagAmounts(db.tweet.find())
```

### Provide top 10 recorded out of the sorted result. (hint: use sort on the result returned by MapReduce) 

#### index.js
```js
function top10Hashtags(collection) {
	const tweetHashtags = getHashtagAmounts(collection);

	const res = Object.entries(tweetHashtags)
		.sort((y, x) => (x[1] < y[1] ? -1 : x[1] > y[1] ? 1 : 0))
		.slice(0, 10);

	return res;
}
```

#### mongo shell
```js
load('index.js');
```

#### mongo shell
```js
top10Hashtags(db.tweet.find())
```