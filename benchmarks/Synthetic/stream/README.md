STREAM
======

Benchmark Purpose
-----------------

Measure attainable sustained read and write bandwidth of the primary
memory of a single node.


Benchmark Results for Submission
--------------------------------

The tenderer commits to a minimum sustained memory bandwith for
the STREAM triad operation, a[j] = b[j] + s*c[j], for any single
compute node in the system.


Run Rules
---------

* The vector length should be at least 30% of the main memory of the
  node, i.e. the total memory footprint of the three arrays should be
  at least 90% of the main memory of the node.

* The tenderer is allowed to optimize the STREAM operations as long as
  the modified source code and sufficient documentation to carry out
  similar optimization in production code are provided.

* The elements of each vector must be consecutive in virtual address space,
  however non-consecutive allocation of physical pages, e.g. to spread data
  across NUMA domains, is allowed.

* All CPU resources of the node may be utilized, in particular the operation
  may be performed using all available hardware threads and core if desired.

* If present, accelerators, e.g. GPUs, may not be used.


Source Code
-----------

The CPU reference implementation is provided by
[https://www.cs.virginia.edu/stream/FTP/Code/] and included in
the src folder. The SHA-1 checksum of the included file is:

afe4e58ec9ba61eba0b8b65cb24789295f8a539e  stream.c