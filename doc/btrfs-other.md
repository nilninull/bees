[Back to top](../README.md)

Good Btrfs Feature Interactions
-------------------------------

Bees has been tested in combination with the following:

* btrfs compression (zlib, lzo, zstd), mixtures of compressed and uncompressed extents
* PREALLOC extents (unconditionally replaced with holes)
* HOLE extents and btrfs no-holes feature
* Other deduplicators, reflink copies (though Bees may decide to redo their work)
* btrfs snapshots and non-snapshot subvols (RW and RO)
* Concurrent file modification (e.g. PostgreSQL and sqlite databases, build daemons)
* all btrfs RAID profiles (people ask about this, but it's irrelevant to bees)
* IO errors during dedup (read errors will throw exceptions, Bees will catch them and skip over the affected extent)
* Filesystems mounted *with* the flushoncommit option
* 4K filesystem data block size / clone alignment
* 64-bit and 32-bit host CPUs (amd64, x86, arm)
* Large (>16M) extents
* Huge files (>1TB--although Btrfs performance on such files isn't great in general)
* filesystems up to 25T bytes, 100M+ files
* btrfs receive
* btrfs nodatacow/nodatasum inode attribute or mount option (bees skips all nodatasum files)
* open(O_DIRECT) (seems to work as well--or as poorly--with bees as with any other btrfs feature)

Bad Btrfs Feature Interactions
------------------------------

Bees has been tested in combination with the following, and various problems are known:

* bcache, lvmcache:  **severe (filesystem-destroying) metadata corruption
  issues** observed in testing and reported by users, apparently only when
  used with bees.  Plain SSD and HDD seem to be OK.
* btrfs send:  sometimes aborts with an I/O error when bees changes the
  data layout during a send.  The send can be restarted and will work
  if bees has finished processing the snapshot being sent.  No data
  corruption observed other than the truncated send.
* btrfs qgroups:  very slow, sometimes hangs
* btrfs autodefrag mount option:  hangs and high CPU usage problems
  reported by users.  bees cannot distinguish autodefrag activity from
  normal filesystem activity and will likely try to undo the autodefrag,
  so it should probably be turned off for bees in any case.

Untested Btrfs Feature Interactions
-----------------------------------

Bees has not been tested with the following, and undesirable interactions may occur:

* Non-4K filesystem data block size (should work if recompiled)
* Non-equal hash (SUM) and filesystem data block (CLONE) sizes (probably never will work)
* btrfs seed filesystems (does anyone even use those?)
* btrfs out-of-tree kernel patches (e.g. in-band dedup or encryption)
* btrfs-convert from ext2/3/4 (never tested, might run out of space or ignore significant portions of the filesystem due to sanity checks)
* btrfs mixed block groups (don't know a reason why it would *not* work, but never tested)
* Filesystems mounted *without* the flushoncommit option (don't know the impact of crashes during dedup writes vs. ordinary writes)


---

[Back to top](../README.md)
