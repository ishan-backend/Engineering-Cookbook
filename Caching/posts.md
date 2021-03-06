**Major Cache issues and its solution**

๐๐๐๐ก๐ & ๐ข๐ญ๐ฌ ๐ญ๐ก๐ซ๐๐ ๐ฆ๐๐ข๐ง ๐ฉ๐ซ๐จ๐๐ฅ๐๐ฆ๐ฌ & ๐ฌ๐จ๐ฅ๐ฎ๐ญ๐ข๐จ๐ง๐ฌ <br />
While caching is great, but its careless use can raise 3 main issues๐๐ผ

๐๐๐๐๐ 1๏ธโฃ : ๐๐๐๐ก๐ ๐ฉ๐๐ง๐๐ญ๐ซ๐๐ญ๐ข๐จ๐ง. <br />
The case when the data queried is neither present in database nor in cache. As a result, the request will be directed from cache to database eventually, penetrating the cache layer and defeating the whole purpose of caching. It can be due to some malicious attack. <br />
๐๐จ๐ฅ๐ฎ๐ญ๐ข๐จ๐ง: <br />
๐ก Cache keys with null value. Prefer short TTL (Time to Live) for keys with null value. <br />
๐กUse Bloom filters. Basically it can tell whether a key is present in a set or not very quickly. Then if not present, ignore that calls completely, i.e. such calls should neither hit cache nor database. <br />


๐๐๐๐๐ 2๏ธโฃ : ๐๐๐๐ก๐ ๐๐ฏ๐๐ฅ๐๐ง๐๐ก๐. <br />
If the cache goes down for some reason, the massive query request that was originally blocked by the cache will flock to the database like a mad dog. At this point, if the database canโt withstand this huge pressure, it will collapse. <br />
๐๐จ๐ฅ๐ฎ๐ญ๐ข๐จ๐ง: <br />
๐ก Ensure high availability of cache cluster as preventive measure. <br />
๐ก Use Hystrix, so in case a call fails, it returns default result so as to avoid cascaded failures.<br />


๐๐๐๐๐ 3๏ธโฃ : ๐๐จ๐ญ๐ฌ๐ฉ๐จ๐ญ ๐๐๐ญ๐ ๐ฌ๐๐ญ ๐ข๐ฌ ๐ข๐ง๐ฏ๐๐ฅ๐ข๐. <br />
For very hot data, after expiry of cache TTL, the requests will bombard database between the time of expiry of TTL and re-cache.<br />
๐๐จ๐ฅ๐ฎ๐ญ๐ข๐จ๐ง:<br />
๐ก When a hotspot data fails, only the first database query request is sent to the database, and all other query requests are blocked, thus protecting the database. That can be achieved with cache locks (mutex).<br />
๐ก Set different TTLs to avoid simultaneous failures.<br />
