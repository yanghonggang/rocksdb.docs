### When we need it
When the DB uses tons of gigabytes of memory, that is a higher chance that when accessing data in memory, the program will get data TLB miss, as well as cache misses to retrieve the mapping. When using hash table based indexes and bloom filters, provided by some mem tables and table readers, users are more prone to it. Those indexes and blooms are perfect candidates to be allocated in huge page TLB, since you get relatively worse data locality in those data structures. When you see a high data TLB overheads in data structures supporting huge pate TLB, Consider to give this feature a try.