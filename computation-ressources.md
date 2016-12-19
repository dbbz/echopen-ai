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

In order to launch a job, you will have to write a script containing two parts: a ressource definition and the actual instructions you would like to execute.  The ressource definition describes what kind of machine you want to allocate, the number of cores, the number of GPUs etc. Here is an example that does nothing but a `sleep`. 

```
#!/bin/bash
#SBATCH --time=100:30:00
#SBATCH -N 1
#SBATCH -n 8
#SBATCH --gres=gpu:1
#SBATCH --error=jobs/job.%J.err
#SBATCH --output=jobs/job.%J.out
module load cuda/8.0_test
jupyter notebook /home/dbenbouzid/notebooks
sleep 10000h
```

### Ressources restrictions
- Number of GPUs: 0-2
- Number of cores:  1-6
- If you use a GPU, you can allocate a maximum of 8 cores. 