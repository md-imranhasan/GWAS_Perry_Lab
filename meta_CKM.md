METAL Dir:  /depot/ppaschou/apps/METAL/generic-metal/metal --help

''' bash
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
'''
