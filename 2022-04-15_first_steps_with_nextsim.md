# My first steps with nextsim
Latest update on 2022-04-15.


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

---
## 2. Realistic config from Einar's tutorial (FAILS-15/04/2022):
* Get data from Aurélie (from Einar's SASIP tutorial from Jun 2021) and copy it to  /Users/leroux/DATA/NEXTSIM/CLIMRUN`.
* Then run test:
```
cd /Users/leroux/DATA/NEXTSIM/CLIMRUN
./run_me.sh
```
where run_me.sh is:
```
#! /bin/bash

docker run -it —rm \
        -v /Users/leroux/WORK/DEV/NEXTSIM/nextsim:/nextsim \
        -v /Users/leroux/WORK/DEV/NEXTSIM/nextsim/mesh:/mesh \
        -v /Users/leroux/DATA/NEXTSIM/CLIMRUN:/data \
        -v /Users/leroux/DATA/NEXTSIM/CLIMRUN/experiments:/experiments \
        nextsim \
        mpirun —allow-run-as-root \
        —mca btl_vader_single_copy_mechanism none \
        —mca btl ^openib \
        —mca pml ob1 \
        -np 3 \
        nextsim.exec —config-files=/data/bbm_control.cfg
```

* Doing the above, i get a first error:
```
(base) leroux@mcp-oceannext-03:CLIMRUN>>./run_me.sh 
Reading /data/bbm_control.cfg...
terminate called after throwing an instance of 'std::runtime_error'
terminate called after throwing an instance of 'terminate called after throwing an instance of 'std::runtime_error'
std::runtime_error  what():  unrecognised option 'thermo.h_thin_max'
[616aecfb3749:00013] *** Process received signal ***
```

* Now after removing `h_thin_max=0.3` in  part `[thermo]` of the  namelist `bbm_control.cfg` and copiying the mesh file to `/Users/leroux/WORK/DEV/NEXTSIM/nextsim/mesh` i ran again run_me.sh and got a second error:
```
(base) leroux@mcp-oceannext-03:CLIMRUN>>./run_me.sh 
Reading /data/bbm_control.cfg…
nextsim.exec: symbol lookup error: /nextsim/lib/libbamg.so.1: undefined symbol: _ZN7DataSetC1Ev
-------------------------------------------------------
Primary job  terminated normally, but 1 process returned
a non-zero exit code.. Per user-direction, the job has been aborted.
-------------------------------------------------------
--------------------------------------------------------------------------
mpirun detected that one or more processes exited with non-zero status, thus causing
the job to be terminated. The first process to do so was:

  Process name: [[30101,1],0]
  Exit code:    127
--------------------------------------------------------------------------
```
where it seems that there is a problem with the bamg  library and or the DataSet class? 
I'm guessing this DataSet class is not used in the simpler example config (cf 1.). Is this an error coming that the version of the docker image not being consistent with the latest version of branch develop ? I'll ask Aurélie if she can reproduce this error on her machine.



