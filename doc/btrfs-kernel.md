[Back to top](../README.md)

Recommended kernel version
--------------------------

Linux 4.14.34 or newer.

A Brief List Of Btrfs Kernel Bugs
---------------------------------

Missing features (usually not available in older LTS kernels):

* 3.13: `FILE_EXTENT_SAME` ioctl added.  No way to reliably dedup with
  concurrent modifications before this.
* 3.16: `SEARCH_V2` ioctl added.
* 4.2: `FILE_EXTENT_SAME` no longer updates mtime, can be used at EOF.
* 4.14: `LOGICAL_INO_V2` allows userspace to create forward and backward
  reference maps to entire physical extents with a single ioctl call,
  and raises the limit of 2730 references per extent.

Bug fixes:

* 4.14.29: `WARN_ON(ref->count < 0)` in fs/btrfs/backref.c triggers
  almost once per second.  The `WARN_ON` is incorrect, and is now removed.

Unfixed kernel bugs (as of 4.14.71):

* **Deadlock** when using the same files as src and dst in dedup and
  as src and dst in `rename`.  This sometimes hangs servers that are
  receiving updates to large files with `rsync` and running `bees`
  at the same time.

* **Bad interactions** with other Linux block layers:  bcache and lvmcache
  can fail spectacularly, and apparently only while running bees.
  This is definitely a kernel bug, either in btrfs or the lower block
  layers.  *Avoid using bees with these tools unless you intend to
  debug the kernel.*

* **Slow backrefs** (aka toxic extents): If the number of references to a
  single shared extent within a single file grows above a few thousand,
  the kernel consumes CPU for minutes at a time while holding various
  locks that block access to the filesystem.  Bees avoids this bug by
  measuring the time the kernel spends performing certain operations
  and permanently blacklisting any extent or hash where the kernel
  starts to get slow.  Inside Bees, such blocks are marked as 'toxic'
  hash/block addresses.  Linux kernel v4.14 is better but can still
  have problems.

* **`FILE_EXTENT_SAME` is arbitrarily limited to 16MB**.  This is
  less than 128MB which is the maximum extent size that can be created
  by defrag or prealloc.  Bees avoids feedback loops this can generate
  while attempting to replace extents over 16MB in length.

---

[Back to top](../README.md)
