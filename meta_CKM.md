METAL Dir:  /depot/ppaschou/apps/METAL/generic-metal/metal --help


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
