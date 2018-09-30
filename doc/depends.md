[Back to top](../README.md)

Dependencies
------------

* C++11 compiler (tested with GCC 4.9, 6.2.0, 8.1.0)

  Sorry.  I really like closures and shared_ptr, so support
  for earlier compiler versions is unlikely.

* btrfs-progs (tested with 4.1..4.15.1) or libbtrfs-dev
  (tested with version 4.16.1)

  Needed for btrfs.h and ctree.h during compile.
  Also needed by the service wrapper script.

* libuuid-dev

  This library is only required for a feature that was removed after v0.1.
  The lingering support code can be removed.

* [Linux kernel version](btrfs-kernel.md) gets its own page.

* markdown

* util-linux version that provides `blkid` command for the helper
  script `scripts/beesd` to work

---

[Back to top](../README.md)
