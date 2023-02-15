# OSC-Tutorial
A Tutorial on how to use Ohio Super Computing clusters


## Log in
To log in, download the OSCConnect app for Windows at [this link](https://github.com/OSC/osc-connect/releases/latest), and type in your username and password after opening the app. Then click the 'Console' button on the right to use terminal login.

## File transfer
To transfer file, easiest way is to use the OSCConnect app, type in your username and password, then click the 'SFTP' button on the right to open a FTP style GUI for data transfer.

## Loading software
Similar to Expanse, after entering terminal and login node, use syntax `module load matlab` to load the corresponding software module. To unload a module, use `module unload <modulename>`.

## turning on parallel pool on Owens
```
obj = parcluster('local');
obj.NumWorkers = 2;
parpool(obj,2);
```

## turning off parallel pool
```
poolobj = gcp('nocreate');
delete(poolobj);
```

# requesting resources using interactive session
Go to ondemand.osc.edu, logging in, and starting an interactive desktop session.Look for the navigation bar at the top of the page and select Interactive Apps, then Owens Desktop.Notice that there are a lot of fields, but the most important ones, for now, are cores and the number of hours. This will start a virtual machine with linux type OS.

## submitting job using slurm

For a MATLAB job, create a job script file `script` without extensions with something like the following:
```
#!/bin/bash
#SBATCH --time=2:00:00
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=28
#SBATCH --mem=400gb
#SBATCH --mail-type=ALL
#SBATCH --acount=PAS2187

set -x
module load matlab
matlab -nodisplay < parfor_test.m
```

Then in the login node terminal, type:
```
sbatch script
```
where `script` is the name of the job script file. Note, if you created the script file in Windows OS, there will be a file mismatch, use `dos2unix script` to convert the script into a unix-friendly version before doing `sbatch script`.

Code Explanation I (Resource section):
- `#!/bin/bash` this is called a shabang, it tells the OS that this file is a job script
- `#SBATCH` slurm sbatch directives, giving essential commands on aspects of the resource request, such as resource limit. For example `#SBATCH --time=2:00:00` specifies max time of 2 hours for a job, `#SBATCH --nodes=4` specifies 4 nodes, `#SBATCH --ntasks=28` specifies 28 cores for a sequential job in one node, `#SBATCH --nodes=4 --ntasks-per-node=28` specifies 4 nodes, 28 cores per node, totalling 112 cores.
- `--jobname=my_job_1` this is optionally a name assigned to a job. By default it will be the job script file name.
- `--mail-type` you can specify email notification for `BEGIN`, `END`, `FAIL`  or `ALL` of the job. For example, `--mail-type=END` will notify you when job ends. You do not need to specifiy email address since by default your user email will be used.
- `--output` you can change the name of the log file by using `--output=mylog.out`. If not used, a default log file with name like `slurm-123456.out` will be created instead.
- `--account` specifies account to be charged for the job. To see your eligible accounts, type `OSCfinger nexthybrid` in terminal, where `nexthybrid` is the account name.

Owens specs:
| Owens Parameter | Description |
| --------------- | ----------- |
| CPU             | <= 28 cores |
| Memory          | <= 117gb    |


Code Explanation II (Executable Section)
- `set -x` prints the actual code that is executed in the log file.
- `module load matalb` load the software module.
- `matlab -nodisplay < parfor_test.m` running the program with the correct software.
(TBD, unfinished, go to [here](https://www.osc.edu/supercomputing/batch-processing-at-osc/job-scripts) to continue. Also look [here](https://www.osc.edu/resources/technical_support/supercomputers/owens/owens_programming_environment))