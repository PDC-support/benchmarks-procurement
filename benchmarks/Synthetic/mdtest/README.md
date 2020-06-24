mdtest
======

Purpose of the Benchmark
------------------------

Measure the achievable performance of meta-data only operations from
multiple clients on the parallel file-system.


Benchmark Results for Submission
--------------------------------

The tenderer commits to a minimum sustained meta-data performance
for the following meta-data only operations for a number of files
of at least 90% of the offered capacity of the meta-data storage:

* file creation
* file deletion
* file status query


Run Rules
---------

* No modifications to the source-code are allowed.

* The POSIX-IO interface must be used.

* The benchmark can use multiple nodes up to the size of the delivered
  system.  Node placement may be chosen to maximize performance.


Source Code
-----------

mdtest is part of the IOR benchmark.  The same source code as used in
the IOR benchmark is used for this test.