[Back to top](../README.md)

bees Gotchas
------------

* If a process holds a directory FD open, the subvol containing the
  directory cannot be deleted (`btrfs sub del` will start the deletion
  process, but it will not proceed past the first open directory FD).
  `btrfs-cleaner` will simply skip over the directory *and all of its
  children* until the FD is closed.  Bees avoids this gotcha by closing
  all of the FDs in its directory FD cache every 10 btrfs transactions.

* If a file is deleted while Bees is caching an open FD to the file,
  Bees continues to scan the file.  For very large files (e.g. VM
  images), the deletion of the file can be delayed indefinitely.
  To limit this delay, Bees closes all FDs in its file FD cache every
  10 btrfs transactions.

* If a snapshot is deleted, bees will generate a burst of exceptions
  for references to files in the snapshot that no longer exist.  This
  lasts until the FD caches are cleared.

---
[Back to top](../README.md)
