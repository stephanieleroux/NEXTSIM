# My first steps with nextsim
Latest update on 2022-04-25.


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
I'm guessing this DataSet class is not used in the simpler example test (cf 1.) and this is why it runs successfully while this realistic config does not?  Is this an error coming that the version of the docker image not being consistent with the latest version of branch develop ? I'll ask Aurélie if she can reproduce this error on her machine.

---
## 3. Realistic config from Einar's tutorial (SUCCESS-25/04/2022):

* Get nextsim version from August 2022 (when the tutorial was made initially) :
```
git clone —branch 2.0.0 git@github.com:nansencenter/nextsim.git
```

* build docker image:
```
docker build . -t nextsim —build-arg BASE_IMAGE=nansencenter/nextsim_base:0.5
```

* Copy mesh and check namelist:
```
cd /Users/leroux/DATA/NEXTSIM/CLIMRUN/
cp small_arctic_10km.msh /Users/leroux/WORK/DEV/NEXTSIM/nextsim/mesh/
```
Remove `h_thin_max=0.3` in  namelist `bbm_control.cfg` 

* run Einar’s example (`duration=60` in namelist): SUCCESS!
```
./run_me.sh
```
Stats from the run: **10 min to run 1 day using 3 CPU** on my own laptop (mac OS).

```
(base) leroux@slx:CLIMRUN>>./run_me.sh
Reading /data/bbm_control.cfg...
[INFO] : -----------------------Simulation started on 2022-Apr-25 08:26:07
[INFO] : TIMESTEP= 900 s
[INFO] : DURATION= 2 day(s)
[INFO] :  ---------- progression: ( 0%) ---------- time spent: 00:00:41
[INFO] :  ---------- progression: ( 5%) ---------- time spent: 00:01:50
[INFO] :  ---------- progression: (10%) ---------- time spent: 00:02:53
[INFO] :  ---------- progression: (16%) ---------- time spent: 00:04:06
[INFO] :  ---------- progression: (21%) ---------- time spent: 00:05:09
[INFO] :  ---------- progression: (26%) ---------- time spent: 00:06:10
[INFO] :  ---------- progression: (31%) ---------- time spent: 00:07:17
[INFO] :  ---------- progression: (36%) ---------- time spent: 00:08:09
[INFO] :  ---------- progression: (42%) ---------- time spent: 00:09:06
[INFO] :  ---------- progression: (47%) ---------- time spent: 00:10:10
[INFO] :  ---------- progression: (52%) ---------- time spent: 00:11:04
[INFO] :  ---------- progression: (57%) ---------- time spent: 00:12:03
[INFO] :  ---------- progression: (62%) ---------- time spent: 00:13:00
[INFO] :  ---------- progression: (68%) ---------- time spent: 00:14:07
[INFO] :  ---------- progression: (73%) ---------- time spent: 00:15:05
[INFO] :  ---------- progression: (78%) ---------- time spent: 00:16:02
[INFO] :  ---------- progression: (83%) ---------- time spent: 00:17:08
[INFO] :  ---------- progression: (89%) ---------- time spent: 00:18:04
[INFO] :  ---------- progression: (94%) ---------- time spent: 00:19:16
[INFO] :  ---------- progression: (99%) ---------- time spent: 00:20:12
[INFO] :    =====   Timer results =====   
 Timer name                     | time spent | % of parent | % of total
 Total                          |    0:19:51 |      100.00 |     100.00
   check_fields                 |    0:00:00 |        0.05 |       0.05
   remesh                       |    0:01:00 |        5.11 |       5.11
     checkRegridding            |    0:00:25 |       42.54 |       2.17
     regrid                     |    0:00:34 |       57.34 |       2.93
       checkMoveDrifters_regrid |    0:00:00 |        0.00 |       0.00
       gatherNodalField         |    0:00:00 |        0.03 |       0.00
       adaptMesh                |    0:00:10 |       28.81 |       0.84
       partition                |    0:00:17 |       51.13 |       1.50
       interpFields             |    0:00:06 |       18.59 |       0.54
       Unaccounted for          |    0:00:00 |        1.44 |       0.04
     resetMeshMean              |    0:00:00 |        0.05 |       0.00
     Unaccounted for            |    0:00:00 |        0.07 |       0.00
   checkReload                  |    0:00:57 |        4.80 |       4.80
     bCoord                     |    0:00:14 |       25.07 |       1.20
     checkReloadDatasets        |    0:00:42 |       73.89 |       3.55
       ERA5_elements            |    0:00:18 |       43.51 |       1.54
       topaz_elements           |    0:00:03 |        7.99 |       0.28
       etopo_elements           |    0:00:03 |        7.29 |       0.26
       ERA5_nodes               |    0:00:13 |       32.82 |       1.16
       topaz_nodes              |    0:00:03 |        8.20 |       0.29
       Unaccounted for          |    0:00:00 |        0.19 |       0.01
     Coord                      |    0:00:00 |        0.99 |       0.05
     Unaccounted for            |    0:00:00 |        0.05 |       0.00
   auxiliary                    |    0:00:00 |        0.03 |       0.03
     calcCohesion               |    0:00:00 |        0.10 |       0.00
     calcCoriolis               |    0:00:00 |       94.77 |       0.03
     Unaccounted for            |    0:00:00 |        5.13 |       0.00
   thermo                       |    0:00:44 |        3.70 |       3.70
     fluxes                     |    0:00:32 |       73.84 |       2.73
       ow_fluxes                |    0:00:15 |       47.50 |       1.30
       ia_fluxes                |    0:00:17 |       52.46 |       1.43
       Unaccounted for          |    0:00:00 |        0.03 |       0.00
     slab                       |    0:00:07 |       16.99 |       0.63
     Unaccounted for            |    0:00:04 |        9.17 |       0.34
   dynamics                     |    0:16:32 |       83.32 |      83.32
     explicitSolve              |    0:16:19 |       98.74 |      82.27
       prep elements            |    0:00:32 |        3.36 |       2.76
       prep nodes               |    0:00:05 |        0.56 |       0.46
       sub-time stepping        |    0:15:34 |       95.42 |      78.50
         updateSigma            |    0:06:18 |       40.49 |      31.78
         gradient terms         |    0:03:28 |       22.31 |      17.51
         sub-solve              |    0:03:07 |       20.04 |      15.73
         updateGhosts           |    0:02:35 |       16.59 |      13.03
         move mesh              |    0:00:02 |        0.32 |       0.25
         Unaccounted for        |    0:00:02 |        0.25 |       0.19
       OW smoother              |    0:00:06 |        0.66 |       0.55
       Unaccounted for          |    0:00:00 |        0.00 |       0.00
     update                     |    0:00:12 |        1.26 |       1.05
     Unaccounted for            |    0:00:00 |        0.00 |       0.00
   output                       |    0:00:30 |        2.60 |       2.60
     checkUpdateDrifters        |    0:00:00 |        0.02 |       0.00
     Unaccounted for            |    0:00:30 |       99.98 |       2.60
   Unaccounted for              |    0:00:04 |        0.39 |       0.39
[INFO] : nb regrid total = 4
[INFO] : -----------------------Simulation done on 2022-Apr-25 08:46:20
[INFO] : -----------------------Total time spent:  00:20:30
```

---
### Summary
* Model runs
* But remains the question of why do i need to commentoff `h_thin_max=0.3` in namelist.
* Also the model runs slowly on my laptop with 3 proc (10 min / 1 model day). And freezes when start a 2 months run (memory?).
* AA reproduced all of the above.
