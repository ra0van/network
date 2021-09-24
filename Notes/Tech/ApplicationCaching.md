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
![](https://github.com/donnemartin/system-design-primer/raw/master/images/ONjORqk.png)

The application is responsible for reading and writing from storage. The cache does not interact with storage directly. Application does the following
- Look for entry in cache, resulting in a cache miss
- Load entry from the database
- Add entry to cache
- Return entry

*Memcached is generally used in this manner*

Subsequent read are fast. Only requested data is cached, which avoids filling up the cache with data that isn't requested. 

Disadvantages:
- Each cache miss results in three trips, which can cause a noticeable delay
- 