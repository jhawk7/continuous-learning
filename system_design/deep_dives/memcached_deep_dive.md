# System Design: Memurched Deep Dive

## Background:
- `Memcache` is an open source single server cache
- Facebook pages were making hundreds of individual network requests to heterogeneous data sources where data was read far more frequently than written (needed a caching layer)

- caching layer can:
	- allow separate scaling of cahce vs DB layer
	- increase read speeds
	- shield underlying DB tier from **"thundering heard"** 
	- FB built distributed system using many memcaches to solve this problem (referred to as `"Mencached"`) 

## Look Aside Caching: 
 - client updates cache 
 - having stateless clients perform updates to cache makes system simpler

 (image)

 - cache is invalidated on key updates because it is an `idempotent operation` 

 ## Fan Out Pattern: 
 - application servers may be reading from hundreds of memcache servers at ance 
 - problems:
 	- one slow server can become bottleneck i
 	- `incast congestion` - network switch is flooded with incoming packets, resulting in some being dropped
 	- too many active connections to cache servers (1 per thread) (every webserver reading from every cache) 

 ## High Lv Architectural Overview:
 - key space is split up by **consistent hashing**

(image)

 ## Performance Optimizations:
 - Parallel and batch fetching of keys across and with requests to a server
 - each webserver runs single `"mcrouter"` process - responsible for dealing/maintaining connections to memcache instances (deals with fanout) 
 - avoids one connection per client thread to mencache server, can use external proxy
 - UDP for reads to limit packet overhead
 	- UDP has less info in each packet; faster (no handshake) 
 	- dropped packets get reported to client and treated as cache miss w/o cache update 
 	- TCP used for set/delete aps to ensure retrying • - Mcrouter level flow control to limit open requests • uses sliding window, additive increase & multiplicative Sent to mencache fleet decrease an dropped packets (like top flow control packes lu, but for memcache casos • unlike TCP conns, it applies to all men cache reas Stale Sets & Thundering Herds: client Client Thundering Herd reaping 6) Set x to 2) nad Z writing 3) Sety Cache/< DB 4) invalidie Stale set * of on Cache .naliste 1) Wate Sol: Leases: - 9 compare and set Concurrent readers - all reads are K Misses (current) and flood DB w/ reads • Heys have tokers obtained by clients. that make all other readers wait for the client holding the tomes to set the Mey in the cache after reading from the db Clike a lock); writer can invalidate prevents thundering herd of reads overwheloon, the 26 1) Cache Miss 2) hen 766 8) white rejected 7) read Cache Token : invalidate (removes taken to prevent 4) wait DB 5) conve ← writer state write