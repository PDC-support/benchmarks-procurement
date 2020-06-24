GPCNET
======


Benchmark Purpose
-----------------

Measure the performance of the fast interconnection network using MPI
under the presence of congestion.


Benchmark Results for Submission
--------------------------------

The tenderer is expect to commit to a minimum performance of the
benchmark for the following test-cases executed under congestion
as produced by the GPCNet load benchmark (network_load_test):

* Host-to-host random-ring two-sided latency
* Host-to-host random-ring two-sided bandwidth
* All-reduce latency

For all cases the results should be given for the 99% percentile.
This value is directly reported in the benchmark output.


Run Rules
---------

* No modifications to the source code are allowed.
* The benchmark must be run on at least 95% of all the compute nodes
  of each of the two modules.
* Each module should be benchmarked in isolation.
* The benchmark must be run with at least 4 processes per 100 Gbit/s
  of nominal injection bandwidth of the node.  For example a node
  with two 200 Gbit/s network interfaces needs to run at least 16 processes
  on each node.


Source Code
-----------

The reference implementation is the officially released version 1.1
which can be obtained from [https://xgitlab.cels.anl.gov/networkbench/GPCNET].
The included source corresponds to tag v1.1
(commit id 9c49d12d10c5f5d4ea71da436def9873eaab6570).
The included file has SHA-1 checksum

0f0b1f836a390ee8a533467af92ec6da4191dfca  GPCNET-v1.1.tar.gz
