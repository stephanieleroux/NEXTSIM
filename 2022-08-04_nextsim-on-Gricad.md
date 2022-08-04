# NeXtSIM on Gricad

### SIngularity
* Rely on a Singularity image build by Aurélie A. from her mac. _[Note: get precise references for the image. Which versions of softwares, BAMG, etc]_ 
* A Singularity image gives you access to a working environment (with a compiler and all dependecies needed, such as BAMG). But you need to compile the neXtSIM code.

### Compile NeXtSIM
* Get an interactive session on dahu:
```
cd /bettik/lerouste/run_nextsim/compil
oarsub -t devel -l /nodes=1/core=1,walltime=00:30:00 --project pr-data-ocean -I
```
* Source environment (set links to establish between local directory and the Singularity image):
```
source ./env_dahu_compil.src 
```
```
vi ./env_dahu_compil.src 
# location of config files and master scripts
export NEXTSIM_CODE_DIR=/bettik/lerouste/nextsim/

# singularity image
export NEXTSIM_IMAGE_NAME=/bettik/alberta/runs_nextsim/nextsim.sif

#bind paths
export SINGULARITY_BIND="$NEXTSIM_CODE_DIR:/nextsim"

```
