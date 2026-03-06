#### Our plan is to do liftiover by crossmap (liftover hg37)(all data should be hg37 , and than do meta analysis BMI: For AFR, metaanalyze the two files with AFR prefixes. For EUR, meta-analyze the remaining

``` bash

# 1. Create the final output folder
mkdir -p /depot/ppaschou/data/NEW_CKM/qc/BMI/meta_ready_hg19

# 2. Filter MAF > 1% and convert to BED format
zcat /depot/ppaschou/data/NEW_CKM/SUMSTATS_IMRAN/BMI/GCST90692054.h.tsv.gz | awk 'BEGIN{FS=OFS="\t"} NR>1 && $7>0.01 && $7<0.99 {print "chr"$1, $2-1, $2, $3, $4, $5, $6, $7, $8, $9}' | gzip > /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_maf1.bed.gz
#module load biocontainers
#module load crossmap
# 3. Liftover from hg38 to hg19
CrossMap.py bed --chromid s /depot/ppaschou/data/NEW_CKM/qc/hg38ToHg19.over.chain.gz /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_maf1.bed.gz /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_hg19.bed

# 4. Format back to TSV with the original header and save directly into the final folder
(echo -e "chromosome\tbase_pair_location\teffect_allele\tother_allele\tbeta\tstandard_error\teffect_allele_frequency\tp_value\trsid" && awk 'BEGIN{FS=OFS="\t"} {print $1, $3, $4, $5, $6, $7, $8, $9, $10}' /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_hg19.bed) | gzip > /depot/ppaschou/data/NEW_CKM/qc/BMI/meta_ready_hg19/GCST90692054_hg19_final.tsv.gz

# 5. Clean up the intermediate and unmapped files
rm /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_maf1.bed.gz /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_hg19.bed /depot/ppaschou/data/NEW_CKM/qc/BMI/GCST90692054_hg19.bed.unmap
```
