# NeXtSIM on Gricad

last update: 2022-08-04.

### SIngularity
* Rely on a Singularity image build by Aurélie A. from her mac.
* A Singularity image gives you access to a working environment (with a compiler and all dependecies needed, such as BAMG). But you need to compile the neXtSIM code.

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

# singularity image
export NEXTSIM_IMAGE_NAME=/bettik/alberta/runs_nextsim/nextsim.sif

#bind paths
export SINGULARITY_BIND="$NEXTSIM_CODE_DIR:/nextsim"

```
* Then run a Shell in the Singularity image, go in the model subdirectory, and run `make`:
```
singularity shell $NEXTSIM_IMAGE_NAME

cd /nextsim/model
make fresh -j8
```

