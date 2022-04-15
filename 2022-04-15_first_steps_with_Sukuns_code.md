# My first steps with Sukun's code to perturb the atmospheric forcing
Latest update on 2022-04-15.

### 1. My starting point: Sukun's tutorial:
 https://github.com/nansencenter/nextsim/tree/IOPerturbation-fram-compile/modules/enkf/offline_perturbations

### 2. Copied files on French HPC Jean Zay for testing
I decided to test this code on jean zay because it is a fortran code with different libraries to link, and this is pretty easy to do on jean zay as many libraries are alreay installed and you don't need to know each path to the -I and -L compiler options as it is implicit.

### 3. Compiled code with adapted makefile.
* i simplified the makefile so that it works on jean zay (removed the -I and -L options in `FLIBS=`) and adapted to the name of the intel compiler on jean zay. Also had to remove the `-lsz` lib in the compiler line, otherwise got error 'lib not found' (also see discussion about that in section 7. below)
```
FLIBS = -lfftw3 -lnetcdf -lnetcdff -lhdf5_hl -lhdf5  -lz -ldl -lm -liomp5 -lpthread -lcurl 
#-lsz
```

### 4. On jean zay load modules:
```
module load intel-compilers/19.0.4
module load hdf5/1.10.5-mpi
module load netcdf/4.7.2-mpi
module load netcdf-fortran/4.5.2-mpi
module load fftw/3.3.8-mpi
```

### 5. Create missing directories for outputs
```
cd /gpfswork/rech/cli/regi915/NEXTSIM/offline_perturbation/
mkdir ./objs/
mkdir ./results/
mkdir ./bin/

```

### 6. Compile and execute script : SUCCESS (2022-04-15):
(i ran this interactively here for the test)
```
#!/bin/bash
work_path=$(cd `dirname $0`;pwd)
echo "work_path=" $work_path

# # compile the code 
 [ ! -d bin ] && mkdir bin
 [ ! -d objs ] && mkdir objs
 rm -rf $work_path/result/*
 cd $work_path/src
 make clean; make

 echo $work_path

# create random perturbations
ensemble_size=3

ulimit -s 2000000 # set sufficient stack  https://stackoverflow.com/questions/66034666/ulimit-stack-size-through-slurm-script
  
for (( i=1; i<=${ensemble_size}; i++ )); do
    mem_path=${work_path}/result/mem$i
    mkdir -p $mem_path
    cd $mem_path
    cp $work_path/bin/p_pseudo2D . 
    cp $work_path/pseudo2D.nml .
    ./p_pseudo2D
    echo "Finish $i over ${ensemble_size} members"
done

```

---
### 7. My questions to Sukun (and his answers):

**(a) Regarding the compilation of the fortran libraries (in the makefile):**
Your compilation line (with mpiifort) includes:

`-L/cluster/software/netCDF-Fortran/4.5.2-iimpi-2019b/lib -lfftw3 -lnetcdf -lnetcdff -lhdf5_hl -lhdf5 -lsz -lz -ldl -lm -liomp5 -lpthread -lcurl`
It works for me (compilation and run) only if I remove the `-lsz` option.
—> Do you have any idea if/what this option `-lsz` is needed for and if it matters if I remove it? (Again, by removing it I am able to compile and run the code apparently successfully).

*Sukun's answer: I think you can remove it as you can compile it successfully. I am not good at compilation on unix. It could depends the specific machine.*

*Laurent B.’s answer: if it compiles and run and the code is not missing any library, it is just fine to remove it.*



**(b) input grid size:**
I need to understand better what is the input grid size (xdim=1024, ydim=1024 in fortran routine main_pseudo2D.F90).:
Are we working on the space grid of the input forcing files or in the space grid of the model?
And why 1024x1024? Is that the exact grid size or an approximation to use a power of 2?

*Sukun's answerA: *We want to add perturbation to the forcing grid. Note the grid has be a polar stereograph grid otherwise it needs to interpolate the raw forcing data to a polar stereograph grid. The perturbation grid size is power of 2, said xdim=2^N, because the perturbation code uses FFT algorithm, which runs fastest if the domain size is power of 2. N should be the integer so that 2^(N-1)< size of forcing grid<= 2^N.* 

**(c) output grid size:**
Why have the output perturbations become 1-dimension in the output files? (I mean dimension xy in the syncforc.nc files) And how is this 1-dimension ‘xy’ then handled by the model?

*Sukun's answer: Using 1-dimension data is easily applied for nextsim data structure and easily saved in this fortran code. The data can export the data in 2-dimension, refering to function saverandfldsynforc() in modrandomforcing.f90*


**(d) usage of the perturbation files:**
More generally, how are these perturbation files then used in a nextsim run? Where could I look at an example code where you have applied those perturbations to the atmospheric forcing? Which GitHub branch should I look at? What part of the code?

*Sukun's answer: *You can search for code segment within # ifdef ENSEMBLE label or function perturbLoadedData() directly in nextsim/model/externaldata.cpp. For example, https://github.com/nansencenter/nextsim/blob/IOPerturbation-fram-compile/model/externaldata.cpp # L233*
---

