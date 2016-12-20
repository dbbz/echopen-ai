# echOpen learning infrastructure

This section explains how to run a learning procedure on our computing infrastructre, while providing some tips and good practices advices.

## On the ROMEO cluster

We benefit from a strong partnership with with [_ROMEO_](https://romeo.univ-reims.fr/), a farm of 200 GPUs hosted by the University of Reims Champagne-Ardenne. Therefore, if you want to run training procedures on this cluster, you will need first to get an account[^1]. 

Once you have a `<username>` on ROMEO, you can connect to the front node

```
ssh <username>@romeo1.univ-reims.fr
```

[^1]: So far, only core echOpen members have access to it, so you you what to do if 

### Launching jobs

_ROMEO_ uses the `Slurm` workload manager. There are three main commands to know in order to start off

* `sbatch <script name>`, for launching scripts.
* `squeue -u <username>`, to display various information about your jobs, like the `<job id>`
* `scancel <job id>`, in order to cancel a job

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

### Ressources constraints

* Number of GPUs per node: 0-2
* Number of cores per node:  1-16
* If the number of cores is greater than 8, you must allocate both GPUs. 

### Futher SBATCH options

```
#SBATCH --mail-user=<name@domain.com>
#SBATCH --mail-type=ALL
```

### Further Slurm commands

* `scontrol show job <job id>`, for a detailed description of a job
* `scancel --interactive --user=<username>`, cancel all the jobs of a specific user
* `srun <command>`, immediate execution of a command

### Environment variables accessible inside jobs

```
SLURM_NPROCS : number of cores
SLURM_NNODES : number of nodes
SLURM_JOB_ID : job id
SLURM_JOB_NODELIST : list of nodes
SLURM_SUBMIT_DIR : source directory for the job
```

### Tensorflow and Cuda8 \(temporary setup\)

`Tensorflow` uses Cuda8, which is not currently deployed on all of the nodes. If you want to use it, you will have to add

```
module load cuda/8.0_test
```

to your script, and restric your job submission to the nodes that actually implement Cuda8. This is done using the `-w` option of `sbatch`, followed by the list of valid nodes

```
sbatch <script name> -w romeo[35,45-51,56,58,63-74,140]
```

### Tips

#### Zsh

During the first connexion, I strongly recommend setting up `ZSH` with \`\`as a productivity booster

```
sh -c "$(curl -fsSL     https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# or
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

then make `ZSH` you default shell with

```
echo "exec /bin/zsh" >> .bash_profile
```



