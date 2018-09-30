[Back to top](../README.md)

Hash Table Sizing
-----------------

Hash table entries are 16 bytes each (64-bit hash, 52-bit block number,
and some metadata bits).  Each entry represents a minimum of 4K on disk.

    unique data size    hash table size    average dedup block size
        1TB                 4GB                  4K
        1TB                 1GB                 16K
        1TB               256MB                 64K
        1TB                16MB               1024K
       64TB                 1GB               1024K

To change the size of the hash table, use 'truncate' to change the hash
table size, delete `beescrawl.dat` so that bees will start over with a
fresh full-filesystem rescan, and restart `bees`.

---

[Back to top](../README.md)
