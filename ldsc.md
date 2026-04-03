
## ldsc

``` bash
hasan128@login00.negishi:[AF] $ munge_sumstats.py --sumstats finngen_R7_I9_AF.gz --N 191205 --snp rsids --a1 alt --a2 ref --p pval --signed-sumstats beta,0
--frq af_alt --merge-alleles /depot/ppaschou/data/CRM_data/ldsc/w_hm3.snplist --out AF_FINNGEN_EUR --chunksize 500000
```


``` bash
ldsc.py --h2 AF_FINNGEN_EUR.sumstats.gz --ref-ld-chr /depot/ppaschou/data/CRM_data/ldsc/eur_w_ld_chr/ --w-ld-chr /depot/ppaschou/data/CRM_data/ldsc/eur_w_ld_chr/ --out AF_h2
```

# Create the Fixed File
Run this to create the clean _fixed.tbl file. This drops any rows with "NA" in the P-value column and floors the tiny P-values to 1e-300.
```
awk -v OFS='\t' 'NR==1 {print $0} NR>1 { if ($10 == "NA" || $10 == "NaN" || $10 == "-") next; if ($10 + 0 == 0 || $10 < 1e-300) $10=1e-300; print $0 }' /scratch/negishi/hasan128/data/meta_analysis/A1C/A1C_ALL_META_1.tbl > /scratch/negishi/hasan128/data/meta_analysis/A1C/A1C_ALL_META_1_fixed.tbl
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






``` bash
ldsc.py --rg A1C_ALL_munged.sumstats.gz,AF_ALL_munged.sumstats.gz,BMI_ALL_munged.sumstats.gz,CRF_ALL_munged.sumstats.gz,DBP_ALL_munged.sumstats.gz,eGFR_ALL_munged.sumstats.gz,HDL_ALL_munged.sumstats.gz,HF_ALL_munged.sumstats.gz,HTN_ALL_munged.sumstats.gz,SBP_ALL_munged.sumstats.gz,STROKE_ALL_munged.sumstats.gz,T2D_ALL_munged.sumstats.gz,TG_ALL_munged.sumstats.gz,TROPO_ALL_munged.sumstats.gz,WC_ALL_munged.sumstats.gz --ref-ld-chr /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/ --w-ld-chr /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/ --out all_15_traits_cross_ancestry_rg
```
