# Synthetic benchmarks

The tender shall include runs or projections of the following
synthetic benchmarks/ problems taken from the latest HPC Challenge
benchmark (http://icl.cs.utk.edu/hpcc/).

It is permitted (and advised) to replace the standard library calls in
following benchmarks with functions from libraries optimized for the
relevant hardware, in this case the libraries need to be included in
the tender.

The following results from HPC Challenge benchmarks are required:

- Single execution stream (e.g. single-core or single-threaded) with a
memory usage of at least 80 percent of the most affine memory island
(of the memory kind fulfilling the main memory requirement): - DGEMM,
HPL, Stream, SingleFFT
- Node execution (i.e. from one subunit which does not need to employ
the interconnect) with a memory usage of at least 80 percent of
available memory.
  - DGEMM, HPL, Stream, SingleFFT
- Half of proposed system with a memory usage of at least 80 percent
  of available memory.
  - DGEMM, HPL, MPIFFT, PTRANS
- Entire proposed system with a memory usage of at least 80 percent of
  available memory.
  - HPL, MPIFFT, PTRANS

The results should be added to the Benchmark matrix spreadsheet
provided. Information on the compiler used (vendor and version)
together with which numerical libraries were used should also be
provided.  In addition the complete output for the runs (excluding
projections) should also be provided.

### DGEMM

Tests double-precision Matrix-Matrix multiplication

### Stream Triad

*Tests memory bandwidth of primary memory*. 

This benchmark computes a[i] = b[i] + s*c[i] for long vectors of
double precision floating point values where each such operation
counts for 24 bytes of data access, i.e. two loads and one store. The
reference implementation of the benchmark can be downloaded from
https://www.cs.virginia.edu/stream/. The vector size used should be at
least 75% of available memory either for the CPU or the
accelerator. Multiple threads can be used to simultaneously process
the requests, the vendor is also allowed to provide optimized
implementations for the benchmark kernel and to optimize data
placement or loop scheduling as long as documentation for the
rationale behind such optimizations is provided so that similar
optimization can be performed on real applications.

### SingleFFT

*Tests speed of the Fast Fourier Transform*. 

This benchmark evaluates the run-time of a single three-dimensional
fast Fourier transformation as provided by either a vendor provided
library or FFTW. The benchmark test-case is a complex valued
double-precision (64-bit) input array of 256x256x256 points that
resides in a single contiguous location in the CPU-accessible memory,
i.e. not on any external accelerator memory and the result has to be
delivered in the same location, i.e. in-place. The FFT library can
pre-plan execution of the transformation as well as request additional
workspace of up to 512 MiB. The benchmark execution should only utilize 
resources available on a single node.

Unlike the other synthetic benchmarks, SingleFFT will be scored like
an application benchmark of the throughput type. See
[benchmark-evaluation.pdf](../../benchmark-evaluation.pdf) for
details, particularly Equation 1.  
One job is considered to be the time it takes to perform the
FFT (i.e. initialization etc. should not be included). To calculate the
figure of merit, i.e. the number of SingleFFT "jobs" that can be
performed on the total system in one day (separately for Phase 1 and
Phase 2), the following formula should be used:  
`n_{jobs} = t_{day} / t * N_{tot}`  
where `t_{day}=86400 s` is the number of seconds per day, `t` is the 
time in seconds that it takes to compute the FFT and `N_{tot}` is the 
total number of nodes tendered. The quantity `n_{jobs}` should be 
reported in the benchmark matrix spreadsheet.

### HPL

High-Performance LINPACK benchmark as required for submission to the
Top500 list, see https://www.top500.org/project/linpack/. This
benchmark measures the performance of dense LU factorization to solve
a large system of linear equations using 64-bit (double precision)
IEEE floating-point arithmetic. The operations count of the employed
algorithm must be 2/3 n^3 + O(n^2) double precision floating-point
operations, the benchmark software must be obtained from
http://www.netlib.org/benchmark/hpl/ and must not be modified. The
vendor is free to supply the required linear algebra and MPI
libraries. The used parameter file, hpl.dat, has to be included in the
response, alternatively, e.g. in the case of performance projections,
a suggested parameter file should be included and the vendor is
obliged to run the HPL benchmark at the time of delivery to
demonstrate that the system meets or exceeds the claimed performance.

### GPCNET

*Global Performance and Congestion Network Test.*

This network benchmark attempts to characterize the interconnection
network under more realistic load conditions, especially in the
presence of interference from simultaneous jobs. It was presented at
SC19 [https://doi.org/10.1145/3295500.3356215] and the benchmark
source can be downloaded from
https://xgitlab.cels.anl.gov/networkbench/GPCNET. Performance for this
benchmark should be stated for running at full system size and all
targets are given for the 99% percentile, i.e. 99% of all executed
tests in the benchmark code must meet or exceed the target. The
following characteristics are requested: Random-ring latency for small
messages (8B) using two-sided MPI communication, random-ring bandwidth
for large messages (128 kiB) using two-sided MPI communication, and
multiple MPI all-reduce operations for small data (8B). For all those
tests we require both isolated performance on the whole system as well
as performance of a random selection of 20% of the system under
congestion from 80% of the system. These cases are as reported by the
default configuration of the “network_test” resp. “network_load_test”
of the benchmark suite. On each node 4 processes for each 100 Gbit/s
of nominal network injection bandwidth, i.e. port speed, have to be
used, i.e. a single 100 Gbit/s port requires 4 processes whereas two
200 Gbit/s ports require the use of 16 processes.


### IOR

*Test read and write speed of a filesystem.*

Maybe ask Mattias for the specific setup that we would like, but I
would propose the following: read and write from multiple clients to a
single file, at least 10% of total node count. The data written should
be at least the aggregate main-memory size of the system or at least
5% of the file-system size. The test should use MPI-IO for a MPI
library provided by the vendor. Write performance should be attainable
for both the case of pre-existing and newly created files. The
benchmark is available from https://github.com/hpc/ior/releases/tag/3.2.1.

### Mdtest

*Tests metadata operations of a filesystem.*

This benchmark is also part of the IOR benchmark program mentioned above. It measures performance of three metadata operations: creating a file, accessing an existing files meta-data using stat(2), and deleting a file. Similar to IOR, this benchmark also uses MPI to start multiple concurrent processes and we aim to use this to measure metadata performance under contention from also at least 10% of total node counts of the total system size. The benchmark is available from https://github.com/hpc/ior/releases/tag/3.2.1.

## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
