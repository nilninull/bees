[Back to top](../README.md)

Command Line Options
--------------------

* --thread-count (-c) COUNT
  * Specify maximum number of worker threads for scanning.  Overrides
    --thread-factor (-C) and default/autodetected values.
* --thread-factor (-C) FACTOR
  * Specify ratio of worker threads to CPU cores.  Overridden by --thread-count (-c).
    Default is 1.0, i.e. 1 worker thread per detected CPU.  Use values
    below 1.0 to leave some cores idle, or above 1.0 if there are more
    disks than CPUs in the filesystem.
* --loadavg-target (-g) LOADAVG
  * Specify load average target for dynamic worker threads.
    Threads will be started or stopped subject to the upper limit imposed
    by thread-factor, thread-min and thread-count until the load average
    is within +/- 0.5 of LOADAVG.
* --thread-min (-G) COUNT
  * Specify minimum number of worker threads for scanning.
    Ignored unless -g option is used to specify a target load.

* --scan-mode (-m) MODE
  * Specify extent scanning algorithm.  Default mode is 0.
    _EXPERIMENTAL_ feature that may go away.
    * Mode 0: scan extents in ascending order of (inode, subvol, offset).
      Keeps shared extents between snapshots together.  Reads files sequentially.
      Minimizes temporary space usage.
    * Mode 1: scan extents from all subvols in parallel.  Good performance
      on non-spinning media when subvols are unrelated.
    * Mode 2: scan all extents from one subvol at a time.  Good sequential
      read performance for spinning media.  Maximizes temporary space usage.

* --timestamps (-t)
  * Enable timestamps in log output.
* --no-timestamps (-T)
  * Disable timestamps in log output.
* --absolute-paths (-p)
  * Paths in log output will be absolute.
* --strip-paths (-P)
  * Paths in log output will have the working directory at Bees startup
    stripped.
* --verbose (-v)
  * Set log verbosity (0 = no output, 8 = all output, default 8).


---
[Back to top](../README.md)
