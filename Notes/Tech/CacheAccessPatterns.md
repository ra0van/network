In-memory caches such as **Memcached** and **Redis** are  key-value stores between your application and your data storage. Since data is stored on RAM, it is much faster than typical database where data is stored on disk. 
RAM is more limited than a disk, so cache invalidation algorithms such as least recently used ([[LRU]]) can help invalidate cold entries and keep 'hot' data in RAM. 

Redis has following additional features : 
- Persistence option
- Built-in data structures such as sorted sets & list

### Four aspects of Caching
- Application output caching
- Proxy output caching
- Application data caching
- Query caching

Suggestions of what to cache:

-   User sessions
-   Fully rendered web pages
-   Activity streams
-   User graph data

### When to Update the cache
Since we can store only a limited amount of data in cache, you'll need to determine which cache update strategy works best for your use case. 

- Cache-aside
- Write-through
- Write-behind
- Refresh-ahead

#### Cache-aside or Lazy Loading
<img src="https://github.com/donnemartin/system-design-primer/raw/master/images/ONjORqk.png" width=600/>

The application is responsible for reading and writing from storage. The cache does not interact with storage directly. Application does the following
- Look for entry in cache, resulting in a cache miss
- Load entry from the database
- Add entry to cache
- Return entry

*Memcached is generally used in this manner*

Subsequent read are fast. Only requested data is cached, which avoids filling up the cache with data that isn't requested. 

Disadvantages:
- Each cache miss results in three trips, which can cause a noticeable delay
- Data can become stale if it is updated in the database only & not in cache. This issue is mitigated by setting TTL which forces an update of the cache entry or by using write-through.

Use-case : This is what we have done at Qwikcilver XnP, where we have a repository layer (client) which is responsible for updating cache & db as well. 

#### Write-through / Read through
![](https://github.com/donnemartin/system-design-primer/raw/master/images/0vBc0hN.png)

The application uses the case as the main data-store, reading and writing data to it, while the cache is responsible for reading and writing through the database.
- Application add/updates entry in cache
- Cache synchronously writes entry to data store
- Return

Example : 

Application code
`set_user(12345, {"foo" : "bar"})`

Cache code
```def set_user(user_id, values):
	user = db.update(user_id, values)
	cache.set(user_id, values)
```


Write-through is a slow operation overall due to the dual write to db & cache. But subsequent reads of just written data are fast. Users are generally more tolerant of latency when updating data than reading data. Data in the cache is not stale.

Disadvantages: write through
- When a new node is created due to failure or scaling, the new node will not cache entries until the entry is updated in the database. Cache-aside in conjunction with write through can mitigate this issue.
- Most data written might never be read, which can be minimized with a TTL. 

#### Write-behind (write-back)
![](https://github.com/donnemartin/system-design-primer/raw/master/images/rgSrvjG.png)

In write-behind, the application does the following :
- Add/update entry in cache
- Asynchronously write entry to the data store, improving write performance

Disadvantages
- There could be data loss if the cache goes down prior to its contents hitting the data store.
- It is more complex to implement write-behind than it is to implement cache-aside or write-through

Refresh ahead

![](https://github.com/donnemartin/system-design-primer/raw/master/images/kxtjqgE.png)

Refresh-ahead can result in reduced latency vs read-through if the cache can accurately predict which items are likely to be needed in the future.

Disadvantages : refresh-ahead
- Not accurately predicting which items are likely to be needed n the future can result in reduced performance without refresh-ahead.

#### Disadvantages : Cache
- Need to maintain consistency between caches and the source of truth such as the database through cache invalidation
- Cache invalidation is a difficult problem, there is additional complexity associated with when to update the cache.
- Need to make application changes such as adding Redis or memcached. 

### Cache Strategy Selection
#### Read-through/Write-through v/s cache-aside :
- RT/WT simplifies application code
- Cache-aside may have blocking behavior
- Cache-aside may be preferable when there are multiple cache updates triggered to the same storage from different cache servers.

#### Write-through v/s Write-behind : 
- Write-behind caching may deliver considerably higher throughput and reduced latency compared to write-through caching
- Implication of write-behind caching is that database updates occur outside of the cache transaction
- write-behind can conflict with an external update