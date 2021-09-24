Caching improves page load times and reduce load on servers and databases. 
Databases often benefit from a uniform distribution of reads and writes across its partitions. Popular items can skew the distribution, causing bottlenecks. Putting a cache in front of database can help absorb uneven loads and spikes in traffic. 

## Different types of caching

- Client Caching
- [[CDN]] Caching
- Web server Caching
- Database Caching
- [[CacheAccessPatterns]]

### Cache Types in web applications

- Local cache
- Replicated cache
- Distributed cache
- Remote cache
- Near cache

### Limitations of caching

- Memory size is limited
- Synchronization complexity
	- consistency between the cached data state and data source's original data
- Durability - correct cache invalidation
- Scalability

## TODO
- [ ] update cache type details from [here](https://www.slideshare.net/tmatyashovsky/from-cache-to-in-memory-data-grid-introduction-to-hazelcast)
- [ ] update cache replacement policies from [here](https://en.wikipedia.org/wiki/Cache_replacement_policies)
- [ ] Build app using elasticsearch
- [ ] Build app using redis