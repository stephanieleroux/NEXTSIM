# My first steps with nextsim

## 1. Simple idealized run from Anton's tutorial (SUCCESS-15/04/2022):

* Get code:
```
cd /Users/leroux/WORK/DEV/NEXTSIM/
git clone git@github.com:nansencenter/nextsim.git
git checkout develop

# build command for mac (otherwise, crashes at runtime)
docker build . -t nextsim --build-arg BASE_IMAGE=nansencenter/nextsim_base:0.5
```

* Get example data:
```
cd /Users/leroux/DATA/NEXTSIM
curl -o coast_10km.msh ftp://ftp.nersc.no/nextsimf/coast_10km.msh
curl -o coast_10km.cfg ftp://ftp.nersc.no/nextsimf/coast_10km.cfg
cp /Users/leroux/WORK/DEV/NEXTSIM/nextsim/mesh/NpsNextsim.mpp /Users/leroux/DATA/NEXTSIM/
```

* Run test:
```
cd /Users/leroux/DATA/NEXTSIM
./run_me_simple.sh
```
where run_me_simple.sh is: 
```
#! /bin/bash
docker run —rm -it -v /Users/leroux/DATA/NEXTSIM:/mesh -v /Users/leroux/DATA/NEXTSIM:/data nextsim \
        mpirun —allow-run-as-root -np 3 \
        —mca btl_vader_single_copy_mechanism none \
        —mca btl ^openib \
        —mca pml ob1 \
        nextsim.exec —config-files=/data/coast_10km.cfg
```
