
# Attempt to edit and recompile the code
(Attempt to edit and recompile the code in the objective to include the ensemble part by sukun)

Latest update on 2022-04-28.


## 1. Recompile model code "in-place"
Following Anton-SASIP tuto:
* Build image:
```
cd /Users/leroux/WORK/DEV/NEXTSIM/nextsim/   #this is from tag 2.0.0
docker build . -t nextsim   # i don't use the anymore
```
* check that the executable is from the docker image:
```
docker run --rm -it nextsim bash
>> inside docker container >> which nextsim.exec
>> inside docker container >> exit
```
* Now re-compile in place the code:
```
docker run --rm -it -v /Users/leroux/WORK/DEV/NEXTSIM/nextsim:/nextsim/ -w /nextsim/model nextsim make
```
Note: Needs to use -v option to mount the local files in the docker container.
Note: Seems important to use `:/nextsim/ ` rather than `:/nextsim` so that the local files are coorectly mounted in the container.
* Check that an new executable has been created locally:
```
docker run --rm -it nextsim bash
>> inside docker container >> cd model/bin/
>> inside docker container >> ls
(base) leroux@slx:bin>>ls
total 4464
-rwxr-xr-x  1 leroux  staff  1751544 Apr 28 13:44 nextsim.exec
```
Note: in the tuto, Anton says that when running the model, the executable will first looked for inside /nextsim/ and then in /usr/bin/ if not found in the first dir. (Does that suppose to run the script from inside /nextsim/? I don't know yet. To be tested).

Next steps to be tested:
- re-run a few days after recompiling the code in place.
- re-run a few days after recompiling the code and adding the compilation key ENSEMBLE (with a minor edit of the code like a print, for example?)

## 2. Re-run the model after recompiling the  model code "in-place"
--> Success.

(Note: no need to increase the number of threads since there is only 2 available on my mac...)

## 3. Edit and re-recompile
* Test 1: 
