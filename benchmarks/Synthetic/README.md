Synthetic Benchmarks
====================

Please refer the the appropriate sub-directories for each of the benchmarks.

The tenderer is required to commit to performance for each of the following
benchmarks:

* [High-Performance Linpack (HPL)](./hpl)
 - Verifying must and should-requirements in section 3.2.1, 3.4.1 and 3.4.2.
* [STREAM triad](./stream)
 - Verifying a should-requirement in section 3.1.4.2.
* [Fast Fourier Transformation (FFT)](./fft)
 - Evaluated in the same way as application benchmarks.
* [GPCNet](./gpcnet)
  - Verifying must-requirements in section 3.1.4.3.2.
* [IOR](./ior)
 - Verifying must-requirements in section 3.3 and 3.5.7.1.1.
* [mdtest](./mdtest)
 - Verifying must-requirements in section 3.3 and 3.5.7.1.1.

It is permitted (and advised) to replace the standard library calls in
following benchmarks with functions from libraries optimized for the
relevant hardware, in this case the libraries need to be included in
the tender.

* HPL
* STREAM
* FFT

Please see the corresponding subdirectories for more detailed information
on the permitted changes.

Please note that different benchmarks have different requirements and weights
as outlined in the request for tender.


Contact
-------

Questions regarding the benchmark(s) must be posted via the "question and 
answer function" in Visma TendSign.

