==================================
PIPE CASES FOR BENCHMARKING OF NEK
==================================
Author of the README: Nicolas Offermans
Last update: 2016-02-12

--------------------------------------------------------
VERY IMPORTANT CHECK UP LIST BEFORE RUNNING A SIMULATION
--------------------------------------------------------
####################
# Before compiling #
####################
- Adapt makenek :
    * Change SOURCE_ROOT
    * Use correct compilers
    * Coarse grid solver choice:
        + Comment/Uncomment the line IFAMG="true" to use XXT/AMG as coarse grid 
          solver.
        + Data files for the AMG have already been created so unless you want 
          new ones, always comment the line IFAMG_DUMP="true"!
 
- PNPN / PNPN-2: make sure that the whole setup is coherent with the method you
  want to use in the SIZE file :
    * PNPN-2 :
        parameter (lx2=lx1-2) 
        parameter (ly2=ly1-2) 
        parameter (lz2=lz1-2) 
    * PNPN :
        parameter (lx2=lx1) 
        parameter (ly2=ly1) 
        parameter (lz2=lz1) 

- In SIZE file:
    * make sure that mxprev is large enough for proper projections. 
      Recommended value: parameter (mxprev=20)
    * check other parameters (lp, lelt,...).

##################
# Before Running #
##################
- If using AMG, make sure that the correct files
    * amg.dat
    * amg_Aff.dat
    * amg_Afp.dat
    * amg_W.dat
  are in the case folder (same folder as the executable 'nek5000').
  Setup files are in folders amg_new and amg_old for each case.

- In the rea file: 
    * change logical switch IFSPLIT (Important!!!)
        + PNPN-2 : 
            F      IFSPLIT   
        + PNPN :
            T      IFSPLIT
    * check parameters 93, 94 and 95 for proper projections. 
      Recommended values : 
        20.0000     P093: Number of previous pressure solns saved 
        5.00000     P094: start projecting velocity after p94 step  
        5.00000     P095: start projecting pressure after p95 step 

- Make sure that restart files are in the case folder (same folder as the 
  executable 'nek5000').

- Check if P065 and number of CPUs are set correctly (see below for details).

- If using chkpoint function for restart (and files starting with rs8xxx), 
  check that pipe.upar and pipe.restart are correctly configured (see below 
  for details). ==> Recommended restart method

- (If using PRESOLVE/RESTART, check the corresponding line in pipe.rea file (as
  well as pipe.upar and pipe.restart - see below for details). ==> this method
  should not be used as it leads to divergence problems)

-----------
Description
-----------
This repository contains 4 different cases of the flow in a pipe at different
Reynolds numbers. Complete description of the test cases can be found in
El Khoury's paper:
http://cfd.spbstu.ru/agarbaruk/c/document_library/DLFE-44502.pdf

Files that are common to all cases are located in the folder src/. The folders
for the different test cases are : 
- ReTau180
- ReTau360
- ReTau550
- ReTau1000

Each case contains 6 additional folders:
- amg_old with AMG setup files from original Matlab code.
- amg_new with AMG setup files from new Matlab code.
- pnpnm2 containing the SIZE, rea, re2, upar, restart and map files for PNPN-2.
- pnpn containing the SIZE, rea, re2, upar, restart and map files for PNPN.
- rs8_restart contaning restart files to use with chkpoint.f.
- reg_start containing the single restart files to use with PRESOLVE/RESTART.

---------
Compiling
---------
- Copy the SIZE file from the correct case folder (ReTau180, 360, 550 or 1000) 
to the src/ folder.
- Modify SIZE file if necessary.
- Modify makenek.
- Compile using makenek.
- Copy the executable 'nek5000' to the corresponding case folder.

-------
Running
-------
Once the executable has been copied, the simulation can be run in the case 
folder. Description of the different files in each case folder:
- pipe.rea :
    * NSTEPS and IOSTEPS can be changed. The rest of the file should be good.
    * When restarting from previous solution, parameter 65 (#iofiles) should be 
      modified. This must be equal to the number of ionodes used to write out 
      the restart files.
      E.g. in the ReTau360 case restart files are given by (x = 1 : 4) 
       rs8pipe0.f0000x 
       ...
       rs8pipe7.f0000x
   OR (depending on the restart method you choose)
       pipe_rs0.f0000X
       ...
       pipe_rs7.f0000x
      It means that 8 ionodes have been used and consequently P065 = 8.
      /!\ The number of CPUS for running the case should also be a multiple
      of this parameter 65 (i.e. lp = k*8 in the SIZE file).
- Depending on the restart method chosen:
    * Using rs8 files :
      + pipe.upar : file required to restart the case from a previous solution.
        This file contains 2 parameters :
        = CHKPTSTEP that defines the interval for writing restart files. 
          /!\ Writing restart files requires 4 time steps. Therefore, if
          CHKPTSTEP=50, then NSTEPS should be at least equal to 53 in the rea 
          file.
          If you don't want to write restart file, just set CHKPTSTEP > NSTEPS.
        = IFCHKPTRST which is a binary variable. If set to true (T) then the
          initial solution will use restart files. Otherwise (F), it uses the IC  
          from usr file.
      + pipe.restart file that contains only a 0 or a 1:
        = if 0 then restart will use the files rs8xxx.f00001 ... rs8xxx.f00004
        = if 1 then restart will use the files rs8xxx.f00005 ... rs8xxx.f00008
    * Using PRESOLVE/RESTART :
      + pipe.upar : make sure that IFCHKPTRST=F and that CHKPTSTEP > NSTEPS if 
        you don't want to write out data.
      + pipe.rea : just use the classic restart option:
         1  PRESOLVE/RESTART OPTIONS  ***** 
         pipe_rs?.f00001
- About AMG: if one wants to use AMG as a coarse grid solver, the required data
files can be found in the folders amg_old or amg_new for each case. amg_old 
contains data obtained using the initial version of the AMG Matlab setup (the
one in the repo as of today). amg_new contains data obtained using the new 
version of the AMG Matlab setup written by J. Lottes (and presented in his 
thesis).
