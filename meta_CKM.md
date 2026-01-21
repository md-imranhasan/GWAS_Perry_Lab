METAL Dir:  /depot/ppaschou/apps/METAL/generic-metal/metal --help
## QC (A!C_qc)
``` bash
# 1) MVP AFR: QC (MAF>=0.01) + standardize to MARKER A1 A2 FREQ BETA SE P N
zcat GCST90475093.h.tsv.gz | awk 'BEGIN{OFS="\t"} NR==1{print "MARKER","A1","A2","FREQ","BETA","SE","P","N"} NR>1 && $7!="NA" && $7>=0.01 && $7<=0.99 {print $8,$3,$4,$7,$5,$6,$8? $8:"NA",$11}' > A1C_MVP_AFR_READY.tsv

```

## BMI


##### AFR

``` bash
awk -F'\t' 'NR==1 {for(i=1; i<=NF; i++) map[$i]=i; print "CHR", "POS", "A1", "A2", "FREQ", "BETA", "SE", "P"} NR>1 {p_val = 10^(-$(map["neglog10_pval_AFR"])); print $(map["chr"]), $(map["pos"]), $(map["alt"]), $(map["ref"]), $(map["af_AFR"]), $(map["beta_AFR"]), $(map["se_AFR"]), p_val}' AFR_continuous-21001-both_sexes-irnt_QC.tsv > AFR_UKB_Ready.tsv
awk 'NR==1{print "UNIQID", $0} NR>1{print $1":"$2, $0}' AFR_UKB_Ready.tsv > UKB_AFR_Fixed.tsv
```

``` bash
awk 'NR==1{print "UNIQID", $0} NR>1{print $1":"$2, $0}' AFR_GCST90502913_QC.tsv > AFR_GCST90502913_QC_fixed.tsv
```

2. Fix UKB AFR (We use the "Ready" file you made earlier)

awk 'NR==1{print "UNIQID", $0} NR>1{print $1":"$2, $0}' AFR_UKB_Ready.tsv > UKB_AFR_Fixed.tsv

The Code
Here is the final, correct METAL script using these two files.
``` bash
cat <<EOF > bmi_afr.metal
# --- Settings ---
SCHEME STDERR
AVERAGEFREQ ON
MINMAXFREQ ON

# --- File 1: MVP AFR (Fixed) ---
# Column 1 is UNIQID (e.g. 1:54490)
MARKER UNIQID
ALLELE effect_allele other_allele
FREQ   effect_allele_frequency
EFFECT beta
STDERR standard_error
PVALUE p_value
PROCESS AFR_GCST90502913_QC_fixed.tsv

# --- File 2: UKB AFR (Fixed) ---
# Column 1 is UNIQID (e.g. 1:11063)
MARKER UNIQID
ALLELE A1 A2
FREQ   FREQ
EFFECT BETA
STDERR SE
PVALUE P
PROCESS AFR_UKB_Ready_Fixed.tsv

# --- Output ---
OUTFILE BMI_AFR_META_ .tbl
ANALYZE
QUIT
EOF
```



##### EUR



``` bash
cat <<EOF > bmi_eur.metal
# --- Settings ---
SCHEME STDERR
AVERAGEFREQ ON
MINMAXFREQ ON

# --- File 1: FINNGEN (EUR) ---
# Format: rsID A1 A2 FREQ BETA SE P N
MARKER rsID
ALLELE A1 A2
FREQ   FREQ
EFFECT BETA
STDERR SE
PVALUE P
WEIGHT N
PROCESS EUR_Finngen_BMI_QC_Ready.tsv

# --- File 2: PULIT (EUR) ---
# Format: rsID A1 A2 FREQ BETA SE P N
MARKER rsID
ALLELE A1 A2
FREQ   FREQ
EFFECT BETA
STDERR SE
PVALUE P
WEIGHT N
PROCESS PULIT_Bmi.giant-ukbb.meta-analysis.combined.23May2018.HapMap2_only_QC_Ready.tsv

# --- File 3: MVP (EUR) ---
# Format: rsID EA OA EAF BETA SE P N
MARKER rsID
ALLELE EA OA
FREQ   EAF
EFFECT BETA
STDERR SE
PVALUE P
WEIGHT N
PROCESS MVP_R4.1000G_AGR.BMI_Mean_INT.EUR.GIA.dbGaP_QC_Ready.tsv

# --- Output ---
OUTFILE BMI_EUR_META_ .tbl
ANALYZE
QUIT
EOF
```
