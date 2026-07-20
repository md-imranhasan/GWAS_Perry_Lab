## The Corrected S-LDSC SLURM Script

######## Create this file (submit_sldsc_cts.sh) in your /scratch/negishi/hasan128/data/magma/ folder and submit it using sbatch.

``` 
#!/bin/bash
#SBATCH -A pdrineas
#SBATCH -p cpu
#SBATCH -q normal
#SBATCH -t 12:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=32G
#SBATCH --job-name=sldsc_H_cts
#SBATCH --error=%x-%J-%u.err
#SBATCH --output=%x-%J-%u.out

# ==========================================
# Partitioned Heritability & Cell-Type Analysis
# ==========================================

# Define Paths
# IMPORTANT: Point this to where your ldsc.py script actually lives!
# If ldsc.py is not in /depot/ppaschou/data/ldsc/, you must update this path.
LDSC_EXEC_DIR="/depot/ppaschou/data/ldsc" 

WORK_DIR="/scratch/negishi/hasan128/data/magma"
cd $WORK_DIR

# Your munged H-Factor sumstats
INPUT_SUMSTATS="H_Factor_LDSC.sumstats.gz"

# S-LDSC Reference Files (Now pointing to your extracted scratch folders)
REF_DIR="/scratch/negishi/hasan128/data/ldsc_ref"
BASELINE_DIR="$REF_DIR/baselineLD_v2.2/baselineLD."
WEIGHTS_DIR="$REF_DIR/weights_hm3_no_hla/weights."
CTS_FILE="$REF_DIR/Multi_tissue_gene_expr/Multi_tissue_gene_expr.txt" # or .cts depending on the extraction

echo "Starting S-LDSC Cell-Type Specific Analysis on SLURM..."
date

# ==========================================
# RUN THE --h2-cts PIPELINE
# ==========================================
# Ensure you have your LDSC conda environment activated before submitting!
# e.g., source activate ldsc

python $LDSC_EXEC_DIR/ldsc.py \
    --h2 $INPUT_SUMSTATS \
    --ref-ld-chr $BASELINE_DIR \
    --out H_Factor_CellType_MultiTissue \
    --ref-ld-chr-cts $CTS_FILE \
    --w-ld-chr $WEIGHTS_DIR

echo "=========================================="
echo "S-LDSC analysis successfully completed!"
date
echo "=========================================="
``` 
