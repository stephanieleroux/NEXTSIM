# Start with NeXtSIM on Gricad (1/2):  Compilation.

last update: 2022-08-04.

### Singularity
* Rely on a Singularity image build by Aurélie A. from her mac.
* A Singularity image gives you access to a working environment (with a compiler and all dependecies needed, such as BAMG). But you need to re-compile the neXtSIM code and all dependencies.

### Compile NeXtSIM 
* Get an interactive session on dahu:
```
cd /bettik/lerouste/run_nextsim/compil
oarsub -t devel -l /nodes=1/core=8,walltime=00:30:00 --project pr-data-ocean -I
```
* Source environment :
```
source ./env_dahu_compil.src 
```
This is to set the variables and links to establish between local directory and the Singularity image:
```
vi ./env_dahu_compil.src 
# location of config files and master scripts
export NEXTSIM_CODE_DIR=/bettik/lerouste/nextsim/
export 

# singularity image
export NEXTSIM_IMAGE_NAME=/bettik/alberta/runs_nextsim/nextsim.sif

#bind paths
export SINGULARITY_BIND="$NEXTSIM_CODE_DIR:/nextsim"

```
* Then run a Shell in the Singularity image, go in the model subdirectory, and run `make`:
```
singularity shell $NEXTSIM_IMAGE_NAME

cd /nextsim
make fresh -j8
```

* To do the same with a job:
```
vi job_compil.sh
#!/bin/bash
#OAR -n compilnextsim
#OAR -l /nodes=1/core=8,walltime=00:30:00
#OAR --stdout compilnextsim_%jobid%.out
#OAR --stderr compilnextsim_%jobid%.err
#OAR --project pr-data-ocean
#OAR -t devel

source /bettik/lerouste/run_nextsim/compil/env_dahu_compil.src

/usr/local/bin/singularity exec $NEXTSIM_IMAGE_NAME bash -c "cd /nextsim && make fresh -j8"
```
and then : `oarsub -S ./job_compil.sh`


### Compile NeXtSIM with ensemble code modifs
* I copied the code from my laptop.
* Added `export USE_ENSEMBLE=1` in the `env_dahu_compil.src`  file (so that the model code is compiled with this compilation key). 
* `ensemble.cpp` and `ensemble.hpp` copied in /nextsim/model instead of being compiled in `modules/enkF/perturbation`
 because i couldnt have it worked.
 
### See next notebook to run test experiments. 
