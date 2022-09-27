# Start with NeXtSIM on Gricad (2/2): run an experiment

last update: 2022-08-04.


## Run dir:
* Set environment variables:
```
# location of config files and master scripts
export NEXTSIM_CONFIG_DIR=/bettik/lerouste/run_nextsim/run_2d_16cores_ens

# location of input data
export NEXTSIM_INPUT_DIR=/summer/sasip/model-configurations/nextsim/small_arctic_10km/files

# location of output data
export NEXTSIM_OUTPUT_DIR=/bettik/lerouste/small_arctic_10km

# path to master scripts
export PATH=$NEXTSIM_CONFIG_DIR:$PATH

# singularity image
export NEXTSIM_IMAGE_NAME=/bettik/alberta/runs_nextsim/nextsim.sif

# all nextsim repos
export NEXTSIM_CODE_DIR=/bettik/lerouste/nextsim

#bind paths
export SINGULARITY_BIND="$NEXTSIM_INPUT_DIR:/data,$NEXTSIM_OUTPUT_DIR:/output,$NEXTSIM_CONFIG_DIR:/config_files,$NEXTSIM_CODE_DIR:/nextsim,$NEXTSIM_CONFIG_DIR:/mesh"
```

* Then run job:
```
#!/bin/bash
#OAR -n nextsim_small_arctic_10km
#OAR -l /nodes=1/core=16,walltime=00:30:00
#OAR --stdout nextsim_small_arctic_10km_%jobid%.out
#OAR --stderr nextsim_small_arctic_10km_%jobid%.err
#OAR --project pr-data-ocean
#OAR -t devel


source env_dahu.src


CMD="mpirun --allow-run-as-root \
        --mca btl_vader_single_copy_mechanism none \
        --mca btl ^openib \
        --mca pml ob1 \
        -np 16 \
        nextsim.exec --config-files=/config_files/bbm_ens.cfg"

/usr/local/bin/singularity exec $NEXTSIM_IMAGE_NAME $CMD
```

* The namelist is `bbm_ens.cfg`

## Notes about restarting the experiment:
* 30 min --> 10 days. Crashed for time limit reached.
* To re-start from restart file (saved in the output dir / restart): set `true` in namelist:
```
[restart]
output_interval=2
write_interval_restart=true
start_from_restart=true
input_path=/output/ens1/restart
basename=20060209T000000Z
```
And also change `time_init=2006-02-09`.

