[Back to top](../README.md)

About Bees
----------

Bees is a block-oriented userspace dedup agent designed to avoid
scalability problems on large filesystems.

Bees is designed to degrade gracefully when underprovisioned with RAM.
Bees does not use more RAM or storage as filesystem data size increases.
The dedup hash table size is fixed at creation time and does not change.
The effective dedup block size is dynamic and adjusts automatically to
fit the hash table into the configured RAM limit.  Hash table overflow
is not implemented to eliminate the IO overhead of hash table overflow.
Hash table entries are only 16 bytes per dedup block to keep the average
dedup block size small.

Bees does not require alignment between dedup blocks or extent boundaries
(i.e. it can handle any multiple-of-4K offset between dup block pairs).
Bees rearranges blocks into shared and unique extents if required to
work within current btrfs kernel dedup limitations.

Bees can dedup any combination of compressed and uncompressed extents.

Bees operates in a single pass which removes duplicate extents immediately
during scan.  There are no separate scanning and dedup phases.

Bees uses only data-safe btrfs kernel operations, so it can dedup live
data (e.g. build servers, sqlite databases, VM disk images).  It does
not modify file attributes or timestamps.

Bees does not store any information about filesystem structure, so it is
not affected by the number or size of files (except to the extent that
these cause performance problems for btrfs in general).  It retrieves such
information on demand through btrfs SEARCH_V2 and LOGICAL_INO ioctls.
This eliminates the storage required to maintain the equivalents of
these functions in userspace.  It's also why bees has no XFS support.

Bees is a daemon designed to run continuously and maintain its state
across crahes and reboots.  Bees uses checkpoints for persistence to
eliminate the IO overhead of a transactional data store.  On restart,
bees will dedup any data that was added to the filesystem since the
last checkpoint.

Bees is used to dedup filesystems ranging in size from 16GB to 35TB, with
hash tables ranging in size from 128MB to 11GB.

How Bees Works
--------------

Bees uses a fixed-size persistent dedup hash table with a variable dedup
block size.  Any size of hash table can be dedicated to dedup.  Bees will
scale the dedup block size to fit the filesystem's unique data size
using a weighted sampling algorithm.  This allows Bees to adapt itself
to its filesystem size without forcing admins to do math at install time.
At the same time, the duplicate block alignment constraint can be as low
as 4K, allowing efficient deduplication of files with narrowly-aligned
duplicate block offsets (e.g. compiled binaries and VM/disk images)
even if the effective block size is much larger.

The Bees hash table is loaded into RAM at startup (using hugepages if
available), mlocked, and synced to persistent storage by trickle-writing
over a period of several hours.  This avoids issues related to seeking
or fragmentation, and enables the hash table to be efficiently stored
on Btrfs with compression (or an ext4 filesystem, or a raw disk, or
on CIFS...).

Once a duplicate block is identified, Bees examines the nearby blocks
in the files where block appears.  This allows Bees to find long runs
of adjacent duplicate block pairs if it has an entry for any one of
the blocks in its hash table.  The stored hash entry plus the block
recently scanned from disk form a duplicate pair.  On typical data sets,
this means most of the blocks in the hash table are redundant and can
be discarded without significant performance impact.

Hash table entries are grouped together into LRU lists.  As each block
is scanned, its hash table entry is inserted into the LRU list at a
random position.  If the LRU list is full, the entry at the end of the
list is deleted.  If a hash table entry is used to discover duplicate
blocks, the entry is moved to the beginning of the list.  This makes Bees
unable to detect a small number of duplicates (less than 1% on typical
filesystems), but it dramatically improves efficiency on filesystems
with many small files.  Bees has found a net 13% more duplicate bytes
than a naive fixed-block-size algorithm with a 64K block size using the
same size of hash table, even after discarding 1% of the duplicate bytes.

---
[Back to top](../README.md)
