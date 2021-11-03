---
layout: page
title: Sbatch example
permalink: soft/orca/sbatch_example
---
```bash
#!/usr/bin/bash

#!/bin/sh
#SBATCH --nodes=1
#SBATCH --tasks-per-node=24
#SBATCH --ntasks-per-node=24
#SBATCH --job-name=cccc
export KMP_STACKSIZE=10G
export OMP_STACKSIZE=10G
export OMP_NUM_THREADS=24,1
export MKL_NUM_THREADS=24

/opt/orca5/orca cccc.inp > cccc.out
```