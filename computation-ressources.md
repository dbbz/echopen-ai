# echOpen learning infrastructure
This section explains how to run a learning procedure on our computing infrastructre, while providing some tips and good practices advices. 

## On ROMEO
We are extremely grateful for having a partnership with [_ROMEO_](https://romeo.univ-reims.fr/) (probably the biggest GPU farm in Europe currently). 
After getting an account and a `<username>` on ROMEO, 

```
ssh <username>@romeo1.univ-reims.fr
```

### Tip
For your first connexion, I strongly recommend using `ZSH` as a productivity booster

```
sh -c "$(curl -fsSL     https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# or
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

then make `ZSH` you default shell with

    echo "exec /bin/zsh" >> .bash_profile

### Launching jobs
_ROMEO_ uses the `Slurm` workload manager. There are three main commands to know in order to start off

- `sbatch <script name>`, for launching scripts.
- `squeue -u <username>`, to display various information about your jobs, like the `<job id>`
- `scancel <job id>`, in order to cancel a job

In order to launch a job, you will have to write a script containing two parts: a ressource definition and the actual instructions you would like to execute.  The ressource definition describes what kind of machine you want to allocate, the number of cores, the number of GPUs etc. every definition starts with `#SBATCH`. Here is an example that does nothing but a `sleep`. 

```
#!/bin/bash
#SBATCH -J "My job"  # the name of the job
#SBATCH --comment "For comments, eg. a description of your job"
#SBATCH --time=24:00:00  # wall time, HH:MM:SS
#SBATCH -N 1  # number of nodes (machines)
#SBATCH -n 8  # number of cores
#SBATCH --gres=gpu:1  # number of GPUs
#SBATCH --error=jobs/job.%J.err  # where the errors are written
#SBATCH --output=jobs/job.%J.out  # where the standard output is written

echo $CUDA_VISIBLE_DEVICES
sleep 24h
```

### Ressources restrictions
- Number of GPUs: 0-2
- Number of cores:  1-6
- If you use a GPU, you can allocate a maximum of 8 cores. 

### Futher SBATCH options
``` 	 
#SBATCH --mail-user=<name@domain.com>
#SBATCH --mail-type=ALL
```

### Further Slurm options
- `scontrol show job <job id>`, for a detailed description of a job
- `scancel --interactive --user=<username>`, cancel all the jobs of a specific user
- 'srun <command>', immediate execution of a command

### ENV variables inside jobs
```
SLURM_NPROCS : number of cores
SLURM_NNODES : number of nodes
SLURM_JOB_ID : job id
SLURM_JOB_NODELIST : list of nodes
SLURM_SUBMIT_DIR : source directory for the job
```


### Tensorflow and Cuda8