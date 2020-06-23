# PyFR benchmarks

This directory contains general information regarding the PyFR code,
installation, and benchmarking considerations.  Information specific
to the PyFR benchmark suite are to be found in the subdirectory
- [NACA0021/](./NACA0021)

## Source code

### Obtaining the source code

PyFR version 1.9.0 must be used for all benchmark runs.
The source code can be downloaded from http://www.pyfr.org/download.php,
but PyFR can also be installed using pip: `pip install pyfr==1.9.0`.

PyFR depends on a number of other packages. These dependencies are listed
on http://www.pyfr.org/user_guide.php, along with installation instructions.

In the Installation guide below, we present a locally tested installation 
procedure.

### License

PyFR is released under the New BSD License, see the LICENSE file 
in the GitHub repository https://github.com/PyFR/PyFR for details.

### Guidelines for Porting and Modifying the Source Code

Modifications to the PyFR source code are acceptable as long as any
modification, for example new compute kernels or optimizations to
existing kernels, must be made available for integration in the
PyFR source code. Modifications must also not be problem-specific.


## Installation guide

Hard requirements for PyFR:
- appdirs >= 1.4.0
- gimmik >= 2.0
- h5py >= 2.6
- mako >= 1.0.0
- mpi4py >= 2.0
- numpy >= 1.8
- pytools >= 2016.2.1

#### OpenCL Backend

The OpenCL backend targets a range of accelerators including GPUs from
AMD and NVIDIA. The backend requires:
- OpenCL
- pyopencl >= 2015.2.4
- [CLBlast](https://github.com/CNugteren/CLBlast) (see below)

#### CUDA Backend

The CUDA backend targets NVIDIA GPUs with a compute capability of 2.0
or greater. The backend requires:

- CUDA >= 4.2
- pycuda >= 2015.1

#### Running in Parallel

To partition meshes for running in parallel it is also necessary to
have one of the following partitioners installed:

- metis >= 5.0
- scotch >= 6.0


#### Example installation 

A convenient way to install PyFR along with its dependencies is to
install Miniconda or virtualenv and use either or both the `conda` and
`pip` package managers.
The following recipe mostly uses conda but all packages can also be installed 
with pip.

- Download the latest Miniconda installer:  
  https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
  (SHA256 hash: 957d2f0f0701c3d1335e3b39f235d197837ad69a944fa6f5d8ad2c686b69df3b)

- Install Miniconda:  
  - `$ bash Miniconda3-latest-Linux-x86_64.sh`
  - Choose path to install: /path/to/pyfr/miniconda3
  - Activate conda environment: `source miniconda3/bin/activate`
- Load compiler and CUDA/OpenCL environments
- Install packages:
  - conda install appdirs
  - conda install h5py
  - conda install mako
  - conda install numpy
  - conda install metis
  - conda install pytools
  - (for OpenCL) pip install pyopencl
  - (for CUDA backend) pip install pycuda
- Install mpi4py from source (no binaries):
  - pip install mpi4py -v --no-deps --no-binary mpi4py
- GiMMiK needs to be installed manually:
  - git clone git@github.com:PyFR/GiMMiK.git
  - cd GiMMiK && python setup.py install
- Install PyFR 1.9.0
  - pip install pyfr==1.9.0
- Install CLBlast
  - cd /path/to/clblast
  - wget https://github.com/CNugteren/CLBlast/archive/1.5.1.tar.gz
  - tar zxf 1.5.1.tar.gz
  - cd CLBlast-1.5.1
  - mkdir build && cd build
  - cmake -DCMAKE_INSTALL_PREFIX=/path/to/clblast/installation -DOPENCL_INCLUDE_DIRS=/path/to/opencl/include  -DOPENCL_LIBRARIES=/path/to/opencl/lib64/libOpenCL.so -DCMAKE_CXX_COMPILER=g++ -DCMAKE_C_COMPILER=gcc ..
  - make install

To run PyFR after this installation:
- export PATH=/path/to/pyfr/miniconda3/bin/:$PATH
- export LD_LIBRARY_PATH=/path/to/clblast/installation/lib64/:$LD_LIBRARY_PATH

## Performance considerations

PyFR uses kernel generators to generate low-level platform-specific code
(see http://www.pyfr.org/developer_guide.php#kernel-generator).
Currently, PyFR has backends for CUDA, OpenCL and OpenMP, but new backends
can be added with very modest programming effort - see the developer guide 
for details (http://www.pyfr.org/developer_guide.php).
However, a new backend requires a Python wrapper similar to PyCUDA for CUDA
(https://mathema.tician.de/software/pycuda/) and PyOpenCL for OpenCL 
(https://mathema.tician.de/software/pyopencl/).

The CUDA backend uses the cuBLAS library while the OpenCL backend uses the 
generic CLBlast library (https://github.com/CNugteren/CLBlast/). 
CLBlast is already tuned for commonly used OpenCL devices - the current list is at 
https://github.com/CNugteren/CLBlast/blob/master/doc/tuning.md
For devices not on this list one needs to compile and run the tuners, 
which is described at https://github.com/CNugteren/CLBlast/blob/master/doc/tuning.md.


## Benchmark cases

The benchmark suite contains one PyFR benchmark case which is adapted
from the publication:  
"High-Order Implicit Large-Eddy Simulations of
Flow over a NACA0021 Aerofoil" AIAA JOURNAL, Vol. 55, No. 7, July 2017
(https://arc.aiaa.org/doi/suppl/10.2514/1.J055304).  
It must be run in two modes: single-precision and double-precision.


Input files can be found under [NACA0021/](./NACA0021). The main files are:
- 3d_naca.ini: Configuration file with input parameters
- naca0021_p4_7c.pyfrm: Mesh file
- naca_7c-100.00.pyfrs: Solution file from previous warm-up run

There are also corresponding files with a smaller mesh which can be used for testing 
purposes:
- naca0021_p4_1c.pyfrm: Mesh file
- naca_1c-100.00.pyfrs: Solution file from previous warm-up run
- naca_1c-100.00.pyfrs: Solution file from previous warm-up run


### Allowed modifications to input files

The mesh and solution files need to be repartitioned for each 
run to correspond to the number of devices used in that run.

Several sections of the configuration file can be updated.
In the section "[backend]"
the "precision" parameter must be set to both single and double
to produce two sets of results. 

The rank-allocator keyword specifies the 
method for MPI rank allocation and can take the values `linear`
or `random`. Normally, `random` degrades performance.

In the "[backend-cuda]" section, which parametrizes the CUDA backend,
the following parameters can be adjusted without affecting the 
correctness of the simulations, but normally only the block sizes 
(block-1d and block-2d) can affect performance:

1. device-id — method for selecting which device(s) to run on: 
   int | round-robin | local-rank
2. gimmik-max-nnz — cutoff for GiMMiK in terms of the number of non-zero entires in a constant matrix: 
   int
3. mpi-type — type of MPI library that is being used: 
   standard | cuda-aware
4. block-1d — block size for one dimensional pointwise kernels: 
   int
5. block-2d — block size for two dimensional pointwise kernels: 
   int, int

In the "[backend-opencl]" section, which parametrizes the OpenCL backend, 
the following parameters can be adjusted without affecting the
correctness of the simulations, but normally only the block sizes
(local-size-1d and local-size-2d) can affect performance:

1. platform-id — for selecting platform id:
   int | string
2. device-type — for selecting what type of device(s) to run on:
   all | cpu | gpu | accelerator
3. device-id — for selecting which device(s) to run on:
   int | string | local-rank
4. gimmik-max-nnz — cutoff for GiMMiK in terms of the number of non-zero entires in a constant matrix:
   int
5. local-size-1d — local work size for one dimensional pointwise kernels:
   int
6. local-size-2d — local work size for two dimensional pointwise kernels:
   int, int


The "tend" parameter under the "[solver-time-integrator]" section
controls how long the simulation is run (starting from 100), and
"dt-out" under "[soln-plugin-writer]" controls the output frequency of
solution files. To get reliable performance numbers from solution
files (see below), it's sufficient to run for around 10 minutes.
Depending on how many nodes/devices are being used, "dt-out" can be
adjusted so that at least two solution files are generated in ~10
minutes (from which performance numbers can be extracted). For
example, with dt-out=0.1 the first two files would be
"naca-100.10.pyfrs" and "naca-100.20.pyfrs".   
For reference, using the OpenMP backend on 64 nodes (2048 cores in
total) on the Beskow Cray XC40 system results a runtime of around 15
minutes between each solutions file when dt-out is set to 0.1.

### Running benchmarks

This benchmark is of the "strong scaling" type and will be evaluated 
on the best performance possible on the suggested system.
Simulations should be run on increasing number of nodes/devices 
until no gain in performance is observed. This
procedure must be performed in both single and double precision,
which is controlled with the `precision` input parameter in the .ini
file. Performance numbers for both cases must be reported (see
below).

A "warm-up" phase has already been run for this case, and the
benchmark runs must be performed through a restart from a mesh
(.pyfrm file) and a solution file (.pyfrs) resulting from the warm-up
run.  To run on `N` devices, the mesh and the solution needs to be
partitioned into `N` parts with the `pyfr partition` command.   
For example, to prepare the mesh and solution for a run on 32 devices, 
the following commands copy the mesh and solutions file
to a given directory and then re-partitions them to the required 
number of devices:
``` 
$ cp naca0021_p4_1c.pyfrm naca-100.00.pyfrs rundir_n32
$ cd rundir_n32
$ pyfr partition 32 naca0021_p4_1c.pyfrm naca-100.00.pyfrs .
``` 

The re-partitioned mesh and solutions file will have the same filenames
as the original meshes (overwriting the files if the current directory is specified).

To run on e.g. 32 devices using mpirun:
```
cd rundir_n32
mpirun -n 32 pyfr restart -b [backend] naca0021_p4_7c.pyfrm naca_7c-100.00.pyfrs 3d_naca.ini
```
where [backend] can be CUDA, OpenCL or a newly implemented backend.


### Obtaining benchmark results

Solution .pyfrs files have a "stats" entry which can be listed by
```
$ h5dump -d stats naca-XXX.XX.pyfrs
```

The total wall-clock time is reported in the variable "wall-time" 
and the number of steps in the "nsteps" variable. 
The performance is quantified by nsteps/walltime and 
can be calculated from the first two solution files as:
Performance = (nsteps_2 - nsteps_1) / (wall-time_2 - wall-time_1)

For the case when dt-out=0.1 (see above), the first two solution files 
will be "naca-100.10.pyfrs" and "naca-100.20.pyfrs". 

These two benchmark cases will be evaluated by the strong-scaling
metric, i.e. how many jobs can be completed on the tendered system in
one day given that only one job runs at the time (one job has access to 
the entire machine). 
**One job for PyFR is considered to be the time it takes to run 
a simulation time of 0.1, e.g. between simulation times 100.10 and 100.20.**

To calculate the number of jobs per day the strong-scaling formula 
must be used:
`n_{jobs} = t_{day} / t`  
where:
 - `t_{day}` = time in a day (86400 s)
 - `t` = average time to run one job in the shortest possible time for this benchmark 
 - n_{jobs} = number of jobs that can be run in one day for this benchmark.

`n_{jobs}` must be entered into the benchmark matrix spreadsheet.




## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
