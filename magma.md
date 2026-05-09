# Below is the clean saved version of the full MAGMA pipeline + plotting code. It uses your final correct approach:

```bash
H GWAS file: H_fullgenome_merged.tsv
Gene IDs: Ensembl ENSG
Gene-location file: Ensembl_GRCh37_GENCODEv37_clean.gene.loc
MAGMA N: estimated from H SE/MAF, no N column added
Gene-set/tissue files: cleaned to match ENSG IDs
```

## Full MAGMA pipeline script
```
nano /scratch/negishi/hasan128/data/magma/run_magma_H_complete_correct.sh
```

```bash
#!/bin/bash
#SBATCH -A pdrineas
#SBATCH -p cpu
#SBATCH -q normal
#SBATCH -t 12:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=32G
#SBATCH --job-name=magma_H_complete
#SBATCH --error=%x-%J-%u.err
#SBATCH --output=%x-%J-%u.out

set -euo pipefail

# ============================================================
# MAGMA pipeline for GenomicSEM H factor
# Gene-based + MSigDB + GTEx DEG tissue enrichment
# ============================================================

MAGMA_DIR="/scratch/negishi/hasan128/data/magma"
OUTDIR="${MAGMA_DIR}/H_MAGMA_cross_outputs_fixed"

MAGMA="${MAGMA_DIR}/tool/magma"
GWAS="${MAGMA_DIR}/H_fullgenome_merged.tsv"
GENELOC="${MAGMA_DIR}/Ensembl_GRCh37_GENCODEv37_clean.gene.loc"

BFILE="/depot/ppaschou/data/NEW_CKM/g1000_eur_afr_autosome_merged"

MSIGDB_RAW="${MAGMA_DIR}/MSigDB_20231Hs_MAGMA.txt"
MSIGDB_CLEAN="${MAGMA_DIR}/MSigDB_20231Hs_MAGMA_ENSG_clean.txt"

GTEX_V8_GENERAL_RAW="${MAGMA_DIR}/gtex_v8_ts_general_DEG.txt"
GTEX_V8_SPECIFIC_RAW="${MAGMA_DIR}/gtex_v8_ts_DEG.txt"
GTEX_V7_GENERAL_RAW="${MAGMA_DIR}/gtex_v7_ts_general_DEG.txt"
GTEX_V7_SPECIFIC_RAW="${MAGMA_DIR}/gtex_v7_ts_DEG.txt"

mkdir -p "${OUTDIR}"
cd "${OUTDIR}"

echo "============================================================"
echo "Starting full MAGMA pipeline for H factor"
date
echo "============================================================"

echo "Checking required files..."

for f in \
  "${MAGMA}" \
  "${GWAS}" \
  "${GENELOC}" \
  "${BFILE}.bed" \
  "${BFILE}.bim" \
  "${BFILE}.fam" \
  "${MSIGDB_RAW}" \
  "${GTEX_V8_GENERAL_RAW}" \
  "${GTEX_V8_SPECIFIC_RAW}" \
  "${GTEX_V7_GENERAL_RAW}" \
  "${GTEX_V7_SPECIFIC_RAW}"
do
  if [ ! -e "$f" ]; then
    echo "ERROR: Missing file: $f"
    exit 1
  fi
done

chmod +x "${MAGMA}"

echo "MAGMA version:"
"${MAGMA}" --version

# ============================================================
# STEP 1: Estimate H effective N from MAF and SE
# Formula: N = 1 / [2 * MAF * (1-MAF) * SE^2]
# Uses only H ~ SNP rows, error == 0, MAF 0.10-0.50
# ============================================================

echo "Estimating H factor N..."

H_N=$(awk '
BEGIN{FS="\t"}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $4>=0.10 && $4<=0.50 && $13>0 {
  n=1/(2*$4*(1-$4)*($13^2))
  sum+=n
  count++
}
END{
  if(count==0){
    print "ERROR_NO_VALID_SNPS"
    exit 1
  }
  printf "%.0f\n", sum/count
}
' "${GWAS}")

echo "${H_N}" > H_estimated_N.txt
echo "Estimated H_N = ${H_N}"

# ============================================================
# STEP 2: Create MAGMA input files
# H_MAGMA_pval.tsv: SNP P
# H_snp_locations.tsv: SNP CHR BP, no header
# ============================================================

echo "Creating MAGMA p-value file..."

awk '
BEGIN{FS=OFS="\t"}
NR==1{
  print "SNP","P"
  next
}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $2>=1 && $2<=22 {
  p=$15
  gsub(/[<>]/,"",p)

  if(p=="NA" || p=="NaN" || p=="." || p=="") next

  p=p+0

  if(p<=0) p=1e-300
  if(p>1) next
  if(p<1e-300) p=1e-300

  print $1,p
}
' "${GWAS}" > H_MAGMA_pval.tsv

echo "Creating MAGMA SNP-location file..."

awk '
BEGIN{FS=OFS="\t"}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $2>=1 && $2<=22 {
  print $1,$2,$3
}
' "${GWAS}" > H_snp_locations.tsv

echo "P-value file SNP count:"
tail -n +2 H_MAGMA_pval.tsv | wc -l

echo "SNP-location file SNP count:"
wc -l H_snp_locations.tsv

# ============================================================
# STEP 3: Clean MSigDB file to keep only ENSG IDs
# This removes URL column and recovers broken concatenated ENSG IDs.
# ============================================================

echo "Cleaning MSigDB ENSG gene-set file..."

awk '
BEGIN{FS="[ \t]+"; OFS="\t"}
{
  delete seen
  out=$1
  line=$0

  while(match(line,/ENSG[0-9]+/)){
    id=substr(line,RSTART,RLENGTH)
    if(!(id in seen)){
      out=out OFS id
      seen[id]=1
    }
    line=substr(line,RSTART+RLENGTH)
  }

  if(split(out,a,OFS)>=2) print out
}
' "${MSIGDB_RAW}" > "${MSIGDB_CLEAN}"

echo "Cleaned MSigDB file:"
wc -l "${MSIGDB_CLEAN}"

# ============================================================
# STEP 4: Clean GTEx DEG files to ENSG IDs
# Converts gene symbols or ENSG-like entries to clean ENSG IDs.
# Removes empty gene-set rows.
# ============================================================

echo "Cleaning GTEx DEG files to ENSG format..."

cd "${MAGMA_DIR}"

for f in \
  "${GTEX_V8_GENERAL_RAW}" \
  "${GTEX_V8_SPECIFIC_RAW}" \
  "${GTEX_V7_GENERAL_RAW}" \
  "${GTEX_V7_SPECIFIC_RAW}"
do
  base=$(basename "$f" .txt)
  out="${MAGMA_DIR}/${base}_ENSG_clean.txt"
  filtered="${MAGMA_DIR}/${base}_ENSG_clean_filtered.txt"

  awk '
  BEGIN{FS="[ \t,;|]+"; OFS="\t"}
  NR==FNR{
    ensg=$1
    sym=$6
    map[ensg]=ensg
    if(sym!="") map[sym]=ensg
    next
  }
  {
    delete seen
    out=$1
    line=$0

    while(match(line,/ENSG[0-9]+/)){
      id=substr(line,RSTART,RLENGTH)
      if(id in map && !(id in seen)){
        out=out OFS id
        seen[id]=1
      }
      line=substr(line,RSTART+RLENGTH)
    }

    for(i=2;i<=NF;i++){
      g=$i
      gsub(/"/,"",g)
      gsub(/\r/,"",g)
      sub(/\.[0-9]+$/,"",g)

      if(g in map && !(map[g] in seen)){
        out=out OFS map[g]
        seen[map[g]]=1
      }
    }

    print out
  }
  ' "${GENELOC}" "$f" > "$out"

  awk 'NF>=2' "$out" > "$filtered"

  echo "Created: $filtered"
  wc -l "$filtered"
done

cd "${OUTDIR}"

# ============================================================
# STEP 5: MAGMA annotation with Ensembl gene locations
# ============================================================

echo "Running MAGMA SNP-to-gene annotation..."

"${MAGMA}" \
  --annotate window=0 \
  --snp-loc H_snp_locations.tsv \
  --gene-loc "${GENELOC}" \
  --out H_Factor_Ensembl

# ============================================================
# STEP 6: MAGMA gene-based analysis
# ============================================================

echo "Running MAGMA gene-based analysis..."

"${MAGMA}" \
  --bfile "${BFILE}" \
  --pval H_MAGMA_pval.tsv use=SNP,P N="${H_N}" \
  --gene-annot H_Factor_Ensembl.genes.annot \
  --out H_Factor_Ensembl

# ============================================================
# STEP 7: MSigDB pathway enrichment
# ============================================================

echo "Running MSigDB pathway enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MSIGDB_CLEAN}" \
  --out H_Factor_Ensembl_MSigDB_20231Hs

# ============================================================
# STEP 8: GTEx DEG tissue enrichment
# ============================================================

echo "Running GTEx v8 general DEG tissue enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v8_ts_general_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v8_general_DEG_clean

echo "Running GTEx v8 specific DEG tissue enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v8_ts_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v8_specific_DEG_clean

echo "Running GTEx v7 general DEG tissue enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v7_ts_general_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v7_general_DEG_clean

echo "Running GTEx v7 specific DEG tissue enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v7_ts_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v7_specific_DEG_clean

# ============================================================
# STEP 9: Save top tables
# ============================================================

echo "Saving top result tables..."

# Top genes
(head -n 1 H_Factor_Ensembl.genes.out && \
 tail -n +2 H_Factor_Ensembl.genes.out | sort -g -k9,9 | head -n 100) \
 > H_Factor_Ensembl_top100_genes.tsv

# Top genes with NSNPS >= 10
(head -n 1 H_Factor_Ensembl.genes.out && \
 tail -n +2 H_Factor_Ensembl.genes.out | awk '$5>=10' | sort -g -k9,9 | head -n 100) \
 > H_Factor_Ensembl_top100_genes_NSNP10.tsv

# Add gene symbols
awk '
BEGIN{FS=OFS="\t"}
NR==FNR{
  sym[$1]=$6
  next
}
FNR==1{
  print $0,"SYMBOL"
  next
}
FNR>1{
  print $0,(($1 in sym && sym[$1]!="") ? sym[$1] : "NA")
}
' "${GENELOC}" H_Factor_Ensembl_top100_genes.tsv \
> H_Factor_Ensembl_top100_genes_with_symbols.tsv

awk '
BEGIN{FS=OFS="\t"}
NR==FNR{
  sym[$1]=$6
  next
}
FNR==1{
  print $0,"SYMBOL"
  next
}
FNR>1{
  print $0,(($1 in sym && sym[$1]!="") ? sym[$1] : "NA")
}
' "${GENELOC}" H_Factor_Ensembl_top100_genes_NSNP10.tsv \
> H_Factor_Ensembl_top100_genes_NSNP10_with_symbols.tsv

# Function for top gene-set/tissue tables
make_top_gsa () {
  input="$1"
  output="$2"

  {
    grep '^#' "$input" || true
    grep '^VARIABLE' "$input" | head -n 1
    awk '!/^#/ && $1!="VARIABLE" && NF>0' "$input" | sort -g -k7,7 | head -n 100
  } > "$output"
}

make_top_gsa H_Factor_Ensembl_MSigDB_20231Hs.gsa.out \
  H_Factor_Ensembl_MSigDB_20231Hs_top100.tsv

for f in H_Factor_Ensembl_GTEx*.gsa.out
do
  base=$(basename "$f" .gsa.out)
  make_top_gsa "$f" "${base}_top100.tsv"
done

echo "Counting significant results..."

echo "Gene-based P < 2.5e-6:"
tail -n +2 H_Factor_Ensembl.genes.out | awk '$9<2.5e-6' | wc -l

echo "MSigDB Bonferroni P < 0.05 / number_of_sets:"
nsets=$(awk '!/^#/ && $1!="VARIABLE" && NF>0' H_Factor_Ensembl_MSigDB_20231Hs.gsa.out | wc -l)
awk -v n="$nsets" '!/^#/ && $1!="VARIABLE" && NF>0 && $7 < 0.05/n' H_Factor_Ensembl_MSigDB_20231Hs.gsa.out | wc -l

echo "GTEx Bonferroni counts:"
for f in H_Factor_Ensembl_GTEx*.gsa.out
do
  nsets=$(awk '!/^#/ && $1!="VARIABLE" && NF>0' "$f" | wc -l)
  count=$(awk -v n="$nsets" '!/^#/ && $1!="VARIABLE" && NF>0 && $7 < 0.05/n' "$f" | wc -l)
  echo "$(basename "$f"): $count"
done

echo "============================================================"
echo "MAGMA pipeline completed successfully"
date
echo "Output directory: ${OUTDIR}"
echo "============================================================"
```
```bash
sbatch /scratch/negishi/hasan128/data/magma/run_magma_H_complete_correct.sh
```

