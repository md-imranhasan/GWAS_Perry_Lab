
# merge_meta_with_dbsnp_to_get_chr_pos (chr37)
``` bash
awk 'BEGIN {OFS="\t"} NR==FNR { if(FNR==1) print $0, "CHR", "POS"; else data[$1]=$0; next } $3 in data { print data[$3], $1, $2; delete data[$3] }' /depot/pdrineas/data/crm/meta_analyses/TROPO_cross_meta1.tbl /scratch/negishi/hasan128/data/harmon_marry_data/dbsnp37_lookup_chr.tsv > TROPO_cross_with_chr_pos.tbl
```
