Fast Fourier Transformation (FFT)
=================================

Benchmark Purpose
-----------------

Measure the performance of computing large fast Fourier transformations
for double-precision data as found in scientific applications.


Benchmark Results for Submission
--------------------------------

The tenderer is expected to commit to a maximum time that a single node
requires to compute a one-dimensional discrete Fourier transformation of
a 1073741824 (i.e. 2^30) value complex input vector.  Separate results for
nodes of module 1 and 2 should be submitted.

Unlike the other synthetic benchmarks, the FFT benchmark will be scored like
an application benchmark of the throughput type. See
[benchmark-description.pdf](../../../benchmark-description.pdf) for
details, particularly Equation 1.  
One job is considered to be the time it takes to perform the
FFT (i.e. initialization etc. should not be included). To calculate the
figure of merit, i.e. the number of FFT "jobs" that can be
performed on the total system in one day (separately for Phase 1 and
Phase 2), the following formula should be used:  
`n_{jobs} = t_{day} / t * N_{tot}`  
where `t_{day}=86400 s` is the number of seconds per day, `t` is the 
time in seconds that it takes to compute the FFT and `N_{tot}` is the 
total number of nodes tendered. The quantity `n_{jobs}` should be 
reported in the benchmark matrix spreadsheet. 


Run Rules
---------

* A high-quality, general-purpose FFT library, should be used to compute the
  transformation.  In particular the norm-wise relative error of a forward
  transformation followed by an inverse transformation should be of
  order O(2 * log(n) * u) with unit-roundoff u = 2^-53.

* The input and output data vectors must be located in contiguous virtual
  address space of the driver process running on the nodes CPU. Optimization
  of data placement in physical memory, e.g. to utilize NUMA domains, is
  permitted as long as the physical memory used has not significantly worse
  performance than the memory of appropriate size to hold the vectors,
  i.e. 32 GiB, with highest performance with respect to CPU accesses.

* All node resources, e.g. CPU cores and accelerators, may be used to compute
  the transformation.  No off-node resources, e.g. resources on other nodes,
  may be used.  Any necessary data-transfer, e.g. to copy input and output
  data to and from accelerator-attached memory, must be included in the
  reported execution time.

* The FFT library may use a setup or planning function, the time of which
  is not included in the reported transformation time.

* Source code modifications are permitted as long as the modified source
  code is provided.  In particular vendors are encouraged to replace the
  used FFTW library with a faster FFT library.


Source Code
-----------

As a reference the "Single FFT" or "Star FFT" tests of the HPC Challenge
Benchmark Suite Version 1.5.0 should be used.  The included source archive
has SHA-1 checksum

c6244d52e720dd28736b6eca730ca84b3707cad1  hpcc-1.5.0.tar.gz