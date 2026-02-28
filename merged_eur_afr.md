```bash

#!/bin/bash
#SBATCH -A pdrineas
#SBATCH -p cpu
#SBATCH -q normal          # or remove this line (normal is default on cpu)
#SBATCH -t 01:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=8G
#SBATCH --job-name=plink_merge
#SBATCH --mail-type=FAIL,BEGIN,END
#SBATCH --error=%x-%J-%u.err
#SBATCH --output=%x-%J-%u.out

module purge
module load plink

plink --bfile g1000_eur_uniq \
      --bmerge g1000_afr_uniq \
      --make-bed \
      --out g1000_eur_afr_autosome_merged_uniq
```
