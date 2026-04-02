
## ldsc

``` bash
hasan128@login00.negishi:[AF] $ munge_sumstats.py --sumstats finngen_R7_I9_AF.gz --N 191205 --snp rsids --a1 alt --a2 ref --p pval --signed-sumstats beta,0
--frq af_alt --merge-alleles /depot/ppaschou/data/CRM_data/ldsc/w_hm3.snplist --out AF_FINNGEN_EUR --chunksize 500000
```


``` bash
ldsc.py --h2 AF_FINNGEN_EUR.sumstats.gz --ref-ld-chr /depot/ppaschou/data/CRM_data/ldsc/eur_w_ld_chr/ --w-ld-chr /depot/ppaschou/data/CRM_data/ldsc/eur_w_ld_chr/ --out AF_h2
```

# A1C_sh.sh
``` bash
#!/bin/bash
#SBATCH --time=24:00:00
#SBATCH -A pdrineas
#SBATCH --mem=32000
#SBATCH --cpus-per-task=1
#SBATCH --partition=cpu
#SBATCH --qos=normal
#SBATCH --job-name=munge_A1C
#SBATCH --output=logs/munge_A1C_%j.out
#SBATCH --error=logs/munge_A1C_%j.err

# -----------------------------------------------------------------------------
# 1. Load your environment
# -----------------------------------------------------------------------------
# LDSC requires Python 2.7. Uncomment and modify the line below if you use conda:
# source activate ldsc
# OR
# module load conda/xxxx && conda activate ldsc
module --force purge
module load biocontainers
module load ldsc
# -----------------------------------------------------------------------------
# 2. Run munge_sumstats.py
# -----------------------------------------------------------------------------
echo "Starting munge_sumstats at $(date)"

munge_sumstats.py \
  --sumstats /scratch/negishi/hasan128/data/meta_analysis/A1C/A1C_ALL_META_1_fixed.tbl \
  --snp MarkerName \
  --a1 Allele1 \
  --a2 Allele2 \
  --p P-value \
  --signed-sumstats Effect,0 \
  --N 997151 \
  --merge-alleles /depot/ppaschou/data/CRM_data/ldsc/w_hm3.snplist \
  --out A1C_ALL_munged \
 --chunksize 500000

echo "Finished munge_sumstats at $(date)"
```
