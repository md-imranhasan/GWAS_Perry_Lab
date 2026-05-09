# Below is the clean saved version of the full MAGMA pipeline + plotting code. It uses your final correct approach:

```bash
H GWAS file: H_fullgenome_merged.tsv
Gene IDs: Ensembl ENSG
Gene-location file: Ensembl_GRCh37_GENCODEv37_clean.gene.loc
MAGMA N: estimated from H SE/MAF, no N column added
Gene-set/tissue files: cleaned to match ENSG IDs
```

#### convert GTEx DEG files to Ensembl IDs

```bash
cd /scratch/negishi/hasan128/data/magma && for f in gtex_v8_ts_general_DEG.txt gtex_v8_ts_DEG.txt gtex_v7_ts_general_DEG.txt gtex_v7_ts_DEG.txt; do out="${f%.txt}_ENSG.txt"; awk 'BEGIN{FS="[ \t]+";OFS="\t"} NR==FNR{ensg=$1; sym=$6; map[ensg]=ensg; if(sym!="") map[sym]=ensg; next} {delete seen; out=$1; for(i=2;i<=NF;i++){g=$i; if(g ~ /^http/) continue; sub(/\.[0-9]+$/,"",g); if(g in map && !(map[g] in seen)){out=out OFS map[g]; seen[map[g]]=1}} print out}' Ensembl_GRCh37_GENCODEv37_clean.gene.loc "$f" > "$out"; echo "Created $out"; done
```

### Re-create GTEx DEG files with stronger ENSG extraction
```bash
cd /scratch/negishi/hasan128/data/magma && for f in gtex_v8_ts_general_DEG.txt gtex_v8_ts_DEG.txt gtex_v7_ts_general_DEG.txt gtex_v7_ts_DEG.txt; do out="${f%.txt}_ENSG_clean.txt"; awk 'BEGIN{FS="[ \t,;|]+";OFS="\t"} NR==FNR{ensg=$1; sym=$6; map[ensg]=ensg; if(sym!="") map[sym]=ensg; next} {delete seen; out=$1; line=$0; while(match(line,/ENSG[0-9]+/)){id=substr(line,RSTART,RLENGTH); if(id in map && !(id in seen)){out=out OFS id; seen[id]=1}; line=substr(line,RSTART+RLENGTH)}; for(i=2;i<=NF;i++){g=$i; gsub(/"/,"",g); gsub(/\r/,"",g); sub(/\.[0-9]+$/,"",g); if(g in map && !(map[g] in seen)){out=out OFS map[g]; seen[map[g]]=1}} print out}' Ensembl_GRCh37_GENCODEv37_clean.gene.loc "$f" > "$out"; echo "Created $out"; done
```

#### Add gene symbols to top genes
##### Your top genes are currently Ensembl IDs. Run this to add gene symbols:

``` bash
cd /scratch/negishi/hasan128/data/magma/H_MAGMA_cross_outputs_fixed && awk 'BEGIN{OFS="\t"} NR==FNR{sym[$1]=$6;next} FNR==1{print $0,"SYMBOL";next} FNR>1{print $0,($1 in sym ? sym[$1] : "NA")}' /scratch/negishi/hasan128/data/magma/Ensembl_GRCh37_GENCODEv37_clean.gene.loc H_Factor_Ensembl_top100_genes.tsv > H_Factor_Ensembl_top100_genes_with_symbols.tsv
```

#### Add symbols to that filtered table:
``` bash
cd /scratch/negishi/hasan128/data/magma/H_MAGMA_cross_outputs_fixed && awk 'BEGIN{OFS="\t"} NR==FNR{sym[$1]=$6;next} FNR==1{print $0,"SYMBOL";next} FNR>1{print $0,($1 in sym ? sym[$1] : "NA")}' /scratch/negishi/hasan128/data/magma/Ensembl_GRCh37_GENCODEv37_clean.gene.loc H_Factor_Ensembl_top100_genes_NSNP10.tsv > H_Factor_Ensembl_top100_genes_NSNP10_with_symbols.tsv
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


# Final Code:


```bash
#!/bin/bash
#SBATCH -A pdrineas
#SBATCH -p cpu
#SBATCH -q normal
#SBATCH -t 12:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=32G
#SBATCH --job-name=magma_H_final
#SBATCH --error=%x-%J-%u.err
#SBATCH --output=%x-%J-%u.out

set -euo pipefail

# ============================================================
# MAGMA Pipeline for GenomicSEM H Super-Factor
# ============================================================
# Input:
#   H_fullgenome_merged.tsv from GenomicSEM userGWAS
#
# Output:
#   1. MAGMA gene-based results
#   2. MSigDB pathway enrichment
#   3. GTEx tissue enrichment
#   4. Top result tables
# ============================================================

# =========================
# USER CONFIGURATION
# =========================

MAGMA_DIR="/scratch/negishi/hasan128/data/magma"
OUTDIR="${MAGMA_DIR}/H_MAGMA_cross_outputs_final"

MAGMA="${MAGMA_DIR}/tool/magma"
GWAS="${MAGMA_DIR}/H_fullgenome_merged.tsv"

BFILE="/depot/ppaschou/data/NEW_CKM/g1000_eur_afr_autosome_merged"

GENELOC_RAW="${MAGMA_DIR}/Ensembl_GRCh37_GENCODEv37.gene.loc"
GENELOC_CLEAN="${MAGMA_DIR}/Ensembl_GRCh37_GENCODEv37_clean.gene.loc"

MSIGDB="${MAGMA_DIR}/MSigDB_20231Hs_MAGMA.txt"

GTEX_V8_GENERAL="${MAGMA_DIR}/gtex_v8_ts_general_DEG.txt"
GTEX_V8_SPECIFIC="${MAGMA_DIR}/gtex_v8_ts_DEG.txt"
GTEX_V7_GENERAL="${MAGMA_DIR}/gtex_v7_ts_general_DEG.txt"
GTEX_V7_SPECIFIC="${MAGMA_DIR}/gtex_v7_ts_DEG.txt"

mkdir -p "${OUTDIR}"
cd "${OUTDIR}"

echo "============================================================"
echo "Starting MAGMA H-factor pipeline"
date
echo "Output directory: ${OUTDIR}"
echo "============================================================"

# =========================
# STEP 0: CHECK FILES
# =========================

echo "[STEP 0] Checking required files..."

for f in \
  "${MAGMA}" \
  "${GWAS}" \
  "${BFILE}.bed" \
  "${BFILE}.bim" \
  "${BFILE}.fam" \
  "${GENELOC_RAW}" \
  "${MSIGDB}" \
  "${GTEX_V8_GENERAL}" \
  "${GTEX_V8_SPECIFIC}" \
  "${GTEX_V7_GENERAL}" \
  "${GTEX_V7_SPECIFIC}"
do
  if [ ! -e "$f" ]; then
    echo "ERROR: Missing file: $f"
    exit 1
  fi
done

chmod +x "${MAGMA}"
"${MAGMA}" --version

# =========================
# STEP 1: ESTIMATE H EFFECTIVE N
# =========================
# Columns in H_fullgenome_merged.tsv:
# SNP=1, CHR=2, BP=3, MAF=4, lhs=7, rhs=9, SE=13,
# Pval_Estimate=15, error=20

echo "[STEP 1] Estimating H effective N..."

H_N=$(
awk 'BEGIN{FS=OFS="\t"}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $4>=0.10 && $4<=0.50 && $13>0 {
  n=1/(2*$4*(1-$4)*($13^2))
  sum+=n
  count++
}
END{
  if(count==0){
    print "ERROR"
  } else {
    printf "%.0f\n", sum/count
  }
}' "${GWAS}"
)

if [ "${H_N}" = "ERROR" ]; then
  echo "ERROR: Could not estimate H_N."
  exit 1
fi

echo "${H_N}" > H_estimated_N.txt
echo "Estimated H_N = ${H_N}"

# =========================
# STEP 2: CREATE MAGMA INPUT FILES
# =========================

echo "[STEP 2] Creating MAGMA SNP/P and SNP-location files..."

awk '
BEGIN{FS=OFS="\t"}
NR==1{
  print "SNP","P"
  next
}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $2>=1 && $2<=22 {
  p=$15
  gsub("<","",p)
  if(p=="NA" || p=="NaN" || p=="." || p=="") next
  p=p+0
  if(p<=0) p=1e-300
  if(p>1) next
  if(p<1e-300) p=1e-300
  print $1,p
}
' "${GWAS}" > H_MAGMA_pval.tsv

awk '
BEGIN{FS=OFS="\t"}
NR>1 && $7=="H" && $9=="SNP" && $20==0 && $2>=1 && $2<=22 {
  print $1,$2,$3
}
' "${GWAS}" > H_snp_locations.tsv

echo "P-value SNP count:"
tail -n +2 H_MAGMA_pval.tsv | wc -l

echo "SNP-location count:"
wc -l H_snp_locations.tsv

# =========================
# STEP 3: CLEAN ENSEMBL GENE LOCATION FILE
# =========================
# Converts IDs like ENSG00000223972.5_4 to ENSG00000223972

echo "[STEP 3] Cleaning Ensembl gene-location file..."

awk '
BEGIN{FS=OFS="\t"}
{
  id=$1
  sub(/[._].*/, "", id)
  len=$4-$3

  if(!(id in best) || len > best[id]){
    best[id]=len
    line[id]=id OFS $2 OFS $3 OFS $4 OFS $5 OFS $6
  }
}
END{
  for(id in line) print line[id]
}
' "${GENELOC_RAW}" | sort -k2,2V -k3,3n > "${GENELOC_CLEAN}"

echo "Clean gene-location file:"
head "${GENELOC_CLEAN}"
wc -l "${GENELOC_CLEAN}"

# =========================
# STEP 4: CLEAN GTEx FILES
# =========================

echo "[STEP 4] Converting GTEx DEG files to clean ENSG format..."

cd "${MAGMA_DIR}"

for f in \
  gtex_v8_ts_general_DEG.txt \
  gtex_v8_ts_DEG.txt \
  gtex_v7_ts_general_DEG.txt \
  gtex_v7_ts_DEG.txt
do
  out="${f%.txt}_ENSG_clean.txt"
  filtered="${f%.txt}_ENSG_clean_filtered.txt"

  awk '
  BEGIN{FS="[ \t,;|]+";OFS="\t"}
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
  ' "${GENELOC_CLEAN}" "$f" > "$out"

  awk 'NF>=2' "$out" > "$filtered"

  echo "Created: ${filtered}"
done

cd "${OUTDIR}"

# =========================
# STEP 5: MAGMA ANNOTATION
# =========================

echo "[STEP 5] Running MAGMA SNP-to-gene annotation..."

"${MAGMA}" \
  --annotate window=0 \
  --snp-loc H_snp_locations.tsv \
  --gene-loc "${GENELOC_CLEAN}" \
  --out H_Factor_Ensembl

# =========================
# STEP 6: MAGMA GENE-BASED ANALYSIS
# =========================

echo "[STEP 6] Running MAGMA gene-based analysis..."

"${MAGMA}" \
  --bfile "${BFILE}" \
  --pval H_MAGMA_pval.tsv use=SNP,P N="${H_N}" \
  --gene-annot H_Factor_Ensembl.genes.annot \
  --out H_Factor_Ensembl

# =========================
# STEP 7: MSigDB PATHWAY ENRICHMENT
# =========================

echo "[STEP 7] Running MSigDB pathway enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MSIGDB}" \
  --out H_Factor_Ensembl_MSigDB_20231Hs

# =========================
# STEP 8: GTEx TISSUE ENRICHMENT
# =========================

echo "[STEP 8] Running GTEx tissue enrichment..."

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v8_ts_general_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v8_general_DEG_clean

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v8_ts_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v8_specific_DEG_clean

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v7_ts_general_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v7_general_DEG_clean

"${MAGMA}" \
  --gene-results H_Factor_Ensembl.genes.raw \
  --set-annot "${MAGMA_DIR}/gtex_v7_ts_DEG_ENSG_clean_filtered.txt" \
  --out H_Factor_Ensembl_GTEx_v7_specific_DEG_clean

# =========================
# STEP 9: SAVE TOP RESULTS
# =========================

echo "[STEP 9] Saving top result tables..."

(head -n 1 H_Factor_Ensembl.genes.out && \
 tail -n +2 H_Factor_Ensembl.genes.out | sort -g -k9 | head -n 100) \
 > H_Factor_Ensembl_top100_genes.tsv

(head -n 1 H_Factor_Ensembl.genes.out && \
 tail -n +2 H_Factor_Ensembl.genes.out | awk '$5>=10' | sort -g -k9 | head -n 100) \
 > H_Factor_Ensembl_top100_genes_NSNP10.tsv

awk '
BEGIN{OFS="\t"}
NR==FNR{
  sym[$1]=$6
  next
}
FNR==1{
  print $0,"SYMBOL"
  next
}
FNR>1{
  print $0,($1 in sym ? sym[$1] : "NA")
}
' "${GENELOC_CLEAN}" H_Factor_Ensembl_top100_genes.tsv \
> H_Factor_Ensembl_top100_genes_with_symbols.tsv

awk '
BEGIN{OFS="\t"}
NR==FNR{
  sym[$1]=$6
  next
}
FNR==1{
  print $0,"SYMBOL"
  next
}
FNR>1{
  print $0,($1 in sym ? sym[$1] : "NA")
}
' "${GENELOC_CLEAN}" H_Factor_Ensembl_top100_genes_NSNP10.tsv \
> H_Factor_Ensembl_top100_genes_NSNP10_with_symbols.tsv

for f in \
  H_Factor_Ensembl_MSigDB_20231Hs.gsa.out \
  H_Factor_Ensembl_GTEx_v8_general_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v8_specific_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v7_general_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v7_specific_DEG_clean.gsa.out
do
  base=$(basename "$f" .gsa.out)

  {
    awk '/^VARIABLE/{print; exit}' "$f"
    awk '!/^#/ && $1!="VARIABLE" && NF>0' "$f" | sort -g -k7 | head -n 100
  } > "${base}_top100.tsv"
done

# =========================
# STEP 10: SUMMARY
# =========================

echo "[STEP 10] Writing run summary..."

{
  echo "MAGMA H-factor run summary"
  echo "Date: $(date)"
  echo "Output directory: ${OUTDIR}"
  echo "Estimated H_N: ${H_N}"
  echo ""

  echo "Input GWAS:"
  echo "${GWAS}"
  echo ""

  echo "PLINK reference:"
  echo "${BFILE}"
  echo ""

  echo "Gene-location file:"
  echo "${GENELOC_CLEAN}"
  echo ""

  echo "Gene-based significance:"
  GENE_N=$(tail -n +2 H_Factor_Ensembl.genes.out | wc -l)
  echo "Total genes: ${GENE_N}"
  echo -n "Genes with P < 2.5e-6: "
  tail -n +2 H_Factor_Ensembl.genes.out | awk '$9 < 2.5e-6' | wc -l
  echo -n "Genes with Bonferroni P < 0.05/${GENE_N}: "
  tail -n +2 H_Factor_Ensembl.genes.out | awk -v n="${GENE_N}" '$9 < 0.05/n' | wc -l
  echo ""

  echo "Gene-set / tissue enrichment:"
  for f in \
    H_Factor_Ensembl_MSigDB_20231Hs.gsa.out \
    H_Factor_Ensembl_GTEx_v8_general_DEG_clean.gsa.out \
    H_Factor_Ensembl_GTEx_v8_specific_DEG_clean.gsa.out \
    H_Factor_Ensembl_GTEx_v7_general_DEG_clean.gsa.out \
    H_Factor_Ensembl_GTEx_v7_specific_DEG_clean.gsa.out
  do
    nsets=$(awk '!/^#/ && $1!="VARIABLE" && NF>0 {n++} END{print n+0}' "$f")
    sig=$(awk -v n="${nsets}" '!/^#/ && $1!="VARIABLE" && NF>0 && $7 < 0.05/n {c++} END{print c+0}' "$f")
    echo "$(basename "$f"): ${sig} significant at P < 0.05/${nsets}"
  done

} > H_Factor_MAGMA_run_summary.txt

echo "============================================================"
echo "MAGMA H-factor pipeline completed successfully"
date
echo "Output directory: ${OUTDIR}"
echo "============================================================"

echo "Main outputs:"
ls -lh \
  H_estimated_N.txt \
  H_Factor_Ensembl.genes.out \
  H_Factor_Ensembl_top100_genes_with_symbols.tsv \
  H_Factor_Ensembl_top100_genes_NSNP10_with_symbols.tsv \
  H_Factor_Ensembl_MSigDB_20231Hs.gsa.out \
  H_Factor_Ensembl_MSigDB_20231Hs_top100.tsv \
  H_Factor_Ensembl_GTEx_v8_general_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v8_specific_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v7_general_DEG_clean.gsa.out \
  H_Factor_Ensembl_GTEx_v7_specific_DEG_clean.gsa.out \
  H_Factor_MAGMA_run_summary.txt
```


# Some details:
# MAGMA H-Factor Pipeline

This script runs MAGMA gene-based, MSigDB pathway, and GTEx tissue enrichment analyses for the GenomicSEM `H` super-factor GWAS.

## Input files

Required files:

```text
/scratch/negishi/hasan128/data/magma/H_fullgenome_merged.tsv
/scratch/negishi/hasan128/data/magma/MSigDB_20231Hs_MAGMA.txt
/scratch/negishi/hasan128/data/magma/gtex_v7_ts_DEG.txt
/scratch/negishi/hasan128/data/magma/gtex_v7_ts_general_DEG.txt
/scratch/negishi/hasan128/data/magma/gtex_v8_ts_DEG.txt
/scratch/negishi/hasan128/data/magma/gtex_v8_ts_general_DEG.txt
/scratch/negishi/hasan128/data/magma/Ensembl_GRCh37_GENCODEv37.gene.loc
/depot/ppaschou/data/NEW_CKM/g1000_eur_afr_autosome_merged.bed
/depot/ppaschou/data/NEW_CKM/g1000_eur_afr_autosome_merged.bim
/depot/ppaschou/data/NEW_CKM/g1000_eur_afr_autosome_merged.fam
```

```text
Main Output:
H_Factor_Ensembl.genes.out
H_Factor_Ensembl_top100_genes_with_symbols.tsv
H_Factor_Ensembl_top100_genes_NSNP10_with_symbols.tsv
H_Factor_Ensembl_MSigDB_20231Hs.gsa.out
H_Factor_Ensembl_MSigDB_20231Hs_top100.tsv
H_Factor_Ensembl_GTEx_v8_general_DEG_clean.gsa.out
H_Factor_Ensembl_GTEx_v8_specific_DEG_clean.gsa.out
H_Factor_Ensembl_GTEx_v7_general_DEG_clean.gsa.out
H_Factor_Ensembl_GTEx_v7_specific_DEG_clean.gsa.out
H_Factor_MAGMA_run_summary.txt
```
