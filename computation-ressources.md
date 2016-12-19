# echOpen learning infrastructure

This section explains how to run a learning procedure on our computing infrastructre, while providing some tips and good practices advices. 

## On ROMEO

We are extremely grateful to our partner, the [ROMEO](https://romeo.univ-reims.fr/) GPU grid for providing us with the necessary infrastructure to learn deep models. 

If you have an account on ROMEO, 

```
ssh <username>@romeo1.univ-reims.fr
```

For your first connexion, I strongly recommend using `ZSH` in order to boost your productivity

```
sh -c "$(curl -fsSL     https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# or

```



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