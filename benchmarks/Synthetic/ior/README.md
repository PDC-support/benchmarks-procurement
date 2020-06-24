IOR
===

Purpose of the Benchmark
------------------------

This benchmark measures the sustained read and write performance
achieved by concurrent access from multiple clients to the parallel
file-system.


Benchmark Results for Submission
--------------------------------

The tenderer commits to a minimum sustained read and write bandwidth
that can be attained for sizes of at least 90% of the offered capacity
of the parallel file-system.


Run Rules
---------

* No modifications to the source-code are allowed.

* The POSIX-IO interface must be used.

* The benchmark can use multiple nodes up to the size of the delivered
  system.  Node placement may be chosen to maximize performance.


Source Code
-----------

The officially released version 3.2.1 of the IOR benchmark available
from [https://github.com/hpc/ior/releases/tag/3.2.1].  The reference
file can also be found in the src directory.  The file has the SHA-1
checksum

0a39e8221552a30b7b07bc6415c7af029e7ca057  ior-3.2.1.tar.gz
