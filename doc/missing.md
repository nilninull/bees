[Back to top](../README.md)

Things You Might Expect That Bees Doesn't Have
----------------------------------------------

* There's no configuration file (patches welcome!).  There are some tunables
hardcoded in the source that could eventually become configuration options.
There's also an incomplete option parser (patches welcome!).

* There's no way to *stop* the Bees daemon.  Use SIGKILL, SIGTERM, or
Ctrl-C for now.  Some of the destructors are unreachable and have never
been tested.  Bees will repeat some work when restarted.

* The Bees process doesn't fork and writes its log to stdout/stderr.
A shell wrapper is required to make it behave more like a daemon.

* There's no facility to exclude any part of a filesystem (patches
welcome).

* PREALLOC extents and extents containing blocks filled with zeros will
be replaced by holes unconditionally.

* Duplicate block groups that are less than 12K in length can take 30%
of the run time while saving only 3% of the disk space.  There should
be an option to just not bother with those.

* There is a lot of duplicate reading of blocks in snapshots.  Bees will
scan all snapshots at close to the same time to try to get better
performance by caching, but really fixing this requires rewriting the
crawler to scan the btrfs extent tree directly instead of the subvol
FS trees.

* Block reads are currently more allocation- and CPU-intensive than they
should be, especially for filesystems on SSD where the IO overhead is
much smaller.  This is a problem for power-constrained environments
(e.g. laptops with slow CPU).

* Bees can currently fragment extents when required to remove duplicate
blocks, but has no defragmentation capability yet.  When possible, Bees
will attempt to work with existing extent boundaries, but it will not
aggregate blocks together from multiple extents to create larger ones.

* It is possible to resize the hash table without starting over with
a new full-filesystem scan; however, this has not been implemented yet.

---
[Back to top](../README.md)
