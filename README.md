BEES
====

Best-Effort Extent-Same, a btrfs dedup agent.

About Bees
----------

Bees is a block-oriented userspace dedup agent designed for large btrfs
filesystems.

Strengths
---------

 * Space-efficient hash table and matching algorithms - about 1 GB hash table per 10 TB unique data (0.1GB/TB)
 * Incremental data processing using btrfs tree search
 * Works with any combination of compressed and uncompressed files
 * Works with live data
 * Works with file sizes ranging from 4K up to VM images
 * Whole-filesystem dedup, including snapshots
 * Constant hash table size, no surprising RAM usage
 * Automatic self-throttling based on system load

Weaknesses
----------

 * Whole-filesystem dedup, no include/exclude filters
 * Runs continuously as a daemon, no graceful start/stop
 * Requires root privilege (or `CAP_SYS_ADMIN`)
 * First run requires temporary disk space
 * Efficiency and performance degrade with many snapshots
 * Constant hash table size, never uses less than configured RAM

Installation and Usage
----------------------

 * [Dependencies](doc/depends.md)
 * [Installation](doc/install.md)
 * [Configuration](doc/config.md)
 * [Command Line Options](doc/options.md)

Further Information
-------------------

 * [How bees works](doc/how-it-works.md)
 * [Missing bees features](doc/missing.md)
 * [bees vs. other btrfs features](doc/btrfs-other.md)
 * [bees vs. btrfs kernel bugs](doc/btrfs-kernel.md)
 * [bees Gotchas](doc/gotchas.md)

Bug Reports and Contributions
-----------------------------

Email bug reports and patches to Zygo Blaxell <bees@furryterror.org>.

You can also use Github:

        https://github.com/Zygo/bees

Copyright & License
-------------------

Copyright 2015-2018 Zygo Blaxell <bees@furryterror.org>.

GPL (version 3 or later).
