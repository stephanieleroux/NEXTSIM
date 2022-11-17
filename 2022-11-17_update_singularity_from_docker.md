Stephanie Leroux

Latest update: 2022-11-17

- - - -
## Objective
* Convert the NeXtSIM Docker image  provided by NERSC  to a SIngularity image that i can use on a HPC server.

- - - -
## Context 
*  My usual workflow is to run neXtSIM on a HPC server (Dahu@GRICAD) which does not allow Docker containers, and favours environments installed via Singularity.
* I also have  a Mac laptop with the new Apple M1 chip (arch: arm64).

- - - -
## Prerequisite
* I  have Docker running on my laptop.
* The  `buildx` command of Docker is now working.  Run :`docker buildx ls` to check if it is.
* Note: Buildx is supposed to come with Docker but in my case, something was missing and buildx was not working. I had to  follow these instructions to (re)install buildx:
```
wget https://github.com/docker/buildx/releases/download/v0.9.1/buildx-v0.9.1.darwin-arm64
chmod a+x buildx-v0.9.1.darwin-arm64

mkdir -p ~/.docker/cli-plugins

mv buildx-v0.9.1.darwin-arm64 ~/.docker/cli-plugins/docker-buildx
```
Now the command `docker buildx version` should show you the buildx version installed, and `docker buildx ls` shows the platforms.
Refs: https://github.com/docker/buildx/releases and  https://gastaud.io/en/article/docker-buildx-upgrade/.

- - - -
## On my Mac laptop: Build Docker image for specific  platform
* Once you have  Docker running and buildx installed.
* Get latest nextsim code in develop branch:
```
mkdir $HOME/n
cd $HOME/n/
git clone git@github.com:nansencenter/nextsim.git
cd nextsim
git checkout develop
```

* Build the docker image (make sure to run this  inside the nextsim repo!)
```
docker buildx build --platform linux/amd64 . -t nextsim_armv7_buildx_explicit --build-arg BASE_IMAGE=nansencenter/nextsim_base 
```

Note that this command  will, by design,  build an image for linux/amd64/X86 architectures, which is _not_ the right architecture of  my arm64/M1 Mac. 
(If you need an image for an M1 mac, do `--platform linux/arm64` instead). 
More info: See tuto there: https://www.docker.com/blog/multi-platform-docker-builds/

- - - -
## On my Mac laptop: convert the above docker image to singularity
* Make use of an “extension” of Docker named quay.io/singularity/docker2singularity to convert the docker image into a singularity image (More info here: https://github.com/singularityhub/docker2singularity):
```
docker run  -v /var/run/docker.sock:/var/run/docker.sock \
-v /Users/leroux/n/img:/output \
--privileged --platform linux/amd64 -t --rm \
quay.io/singularity/docker2singularity \
nextsim_amd64_buildx_explicit
```
--> The command above has created a singularity image `nextsim_amd64_buildx_explicit.sif`

* Then copy it to Dahu@GRICAD:
```
scp2DA nextsim_amd64_buildx_explicit.sif /bettik/lerouste/DATA/Singularity_Images/
```

- - - -
## On Dahu@GRICAD:
* Get latest neXtSIM code in develop branch:
```
mkdir $YOURDIR/n
cd $YOURDIR/n/
git clone git@github.com:nansencenter/nextsim.git
cd nextsim
git checkout develop
```

* Set  environment by running `source env.src` where file  `env.src` is :
```
# location of config files and master scripts
export NEXTSIM_CODE_DIR=/bettik/lerouste/DEVGIT/n_latest/nextsim/

# singularity image
export NEXTSIM_IMAGE_NAME=/bettik/lerouste/DATA/Singularity_Images/nextsim_amd64_buildx_explicit-2022-11-15-8aaf08bf57e0.sif

#bind paths
export SINGULARITY_BIND=“$NEXTSIM_CODE_DIR:/nextsim"
```

* Run script to run the singularity image and re-compile inside the container:
```
#!/bin/bash
#OAR -n compilnextsim
#OAR -l /nodes=1/core=7,walltime=00:30:00
#OAR --stdout compilnextsim_%jobid%.out
#OAR --stderr compilnextsim_%jobid%.err
#OAR --project pr-data-ocean
#OAR -t devel

# source environment
source /bettik/lerouste/DEVGIT/run_nextsim/compil/env_dahu_compil_latestNOV2022.src

# run singularity and run make to compile the model code only
/usr/local/bin/singularity exec $NEXTSIM_IMAGE_NAME bash -c "cd /nextsim/model && make fresh -j 7"               
```
 
