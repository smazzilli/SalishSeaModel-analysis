# An overview of my method to process the SSM (~V4) output netcdf
### Output file locations
The King County model runs are located in the following directories
```
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/wqm_baseline/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/1b_all_sog_wwtp_off/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/1c_all_sog_riv_off/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/1d_small_sog_wwtp_off/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/1e_med_sog_wwtp_off/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/2a_sog_river_0.5times/ssm_output.nc
- /mmfs1/gscratch/ssmc/USRS/PSI/Sukyong/kingcounty/2b_sog_river_2times/ssm_output.nc
```
General descriptions are as follows:
- 1a. Baseline
- 1b. No SOG/NB WWTP
- 1c. No SOG/NB River Loading
- 1d. No BNR<100 WWTP (Only Bellingham WWTP in SOG/NB)
- 1e. No BNR>100 WWTP (No Bellingham WWTP in SOG/NB)
- 2a. 0.5x SOG/NB River Loading
- 2b. 2.0x SOG/NB River Loading

### Overview of processing
These model output files contain the following variables:
- time
- depth
- DOXG
- LDOC
- B1
- B2
- NH4
- NO3
- PO4
- temp
- salinity
- RDOC
- LPOC
- RPOC
- TDIC
- TALK
- pH
- pCO2
### Method and scripts
The script development notebook is called [dev_process_netcdf.ipynb](https://github.com/RachaelDMueller/KingCounty-Rachael/blob/main/notebooks/dev_process_netcdf.ipynb)
It runs out of memory if run with:
```
--ntasks=1 --cpus-per-task=1 --mem-per-cpu=5120M
```
Instead of adding memory, I opted to create a python script and run with a batch script.  The python script is called [process_netcdf.py](https://github.com/RachaelDMueller/KingCounty-Rachael/blob/main/scripts/process_netcdf.py)
and the shell script has the same name: [process_netcdf.sh](https://github.com/RachaelDMueller/KingCounty-Rachael/blob/main/scripts/process_netcdf.sh)
```
#!/bin/bash
 
## job name 
#SBATCH --job-name=process_netcdf
#SBATCH --account=ssmc
#SBATCH --partition=compute
#SBATCH --nodes=1       
#SBATCH --ntasks-per-node=1 
#SBATCH --time=2:00:00 
#SBATCH --mem=175G 
#SBATCH --mail-user=rdmseas@uw.edu

## Modules needed to run
module purge
module load foster/python/miniconda/3.8
source /mmfs1/gscratch/ssmc/USRS/PSI/Rachael/miniconda3/etc/profile.d/conda.sh
conda activate klone_jupyter

echo  starting the run
date

python process_netcdf.py

date
echo run ended
```

It took XX minutes to process the output

The code is setup to save model output to a directory that combines a specified root path and the model variable name. 
There is a subdirectory that contains netcdf for the min 24 hour value at the bottom level, e.g.:
```
/mmfs1/gscratch/ssmc/USRS/PSI/Rachael/projects/KingCounty/data/DOXG
/mmfs1/gscratch/ssmc/USRS/PSI/Rachael/projects/KingCounty/data/DOXG/bottom
```