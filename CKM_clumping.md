```bash
 plink --bfile /depot/ppaschou/data/NEW_CKM/qc/g1000_eur --clump WC_EUR_META_1.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 1e-5 --clump-r2 0.1 --clump-kb 3000 --out WC_EUR_clumped

```
```bash
plink --bfile /depot/ppaschou/data/NEW_CKM/qc/g1000_eur_afr_autosome_merged.reid --clump A1C_ALL_META_1.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 1e-5 --clump-r2 0.1 --clump-kb 3000 --out A1C_ALL_clumped
```


# Using the cross meta result
```bash
plink --bfile /depot/ppaschou/data/NEW_CKM/SUMSTATS_IMRAN/g1000_eur_afr_autosome_merged --clump /scratch/negishi/hasan128/data/manhatton/WC_ALL_META_1_with_chr_pos.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 5e-8 --clump-r2 0.1 --clump-kb 3000 --out /scratch/negishi/hasan128/data/clumping/WC_ALL_clumped
```




```bash
plink \
--bfile /depot/ppaschou/data/NEW_CKM/SUMSTATS_IMRAN/g1000_eur_afr_autosome_merged \
--clump /scratch/negishi/hasan128/data/manhatton/WC_ALL_META_1_with_chr_pos.tbl \
--clump-snp-field MarkerName \
--clump-field P-value \
--clump-p1 5e-8 \
--clump-r2 0.1 \
--clump-kb 3000 \
--clump-range /scratch/negishi/hasan128/data/clumping/glist-hg19 \
--clump-range-border 0 \
--out /scratch/negishi/hasan128/data/clumping/WC_ALL_clumped

```

## One style code using range
```bash
plink --bfile /depot/ppaschou/data/NEW_CKM/SUMSTATS_IMRAN/g1000_eur_afr_autosome_merged --clump /scratch/negishi/hasan128/data/manhatton/WC_ALL_META_1_with_chr_pos.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 5e-8 --clump-r2 0.1 --clump-kb 3000 --clump-range /scratch/negishi/hasan128/data/clumping/glist-hg19 --clump-range-border 0 --out /scratch/negishi/hasan128/data/clumping/WC_ALL_clumped
```





```bash
mkdir -p /scratch/negishi/hasan128/data/clumping/intersect

awk 'NR>1{split($5,a,":"); chr=a[1]; gsub(/^chr/,"",chr); split(a[2],b,"\\.\\."); start=b[1]-1; end=b[2]; print chr"\t"start"\t"end"\t"$2"\t"$3}' /scratch/negishi/hasan128/data/clumping/range_clumping/DBP_range_clumped.clumped.ranges > /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.bed

awk 'NR>1{split($5,a,":"); chr=a[1]; gsub(/^chr/,"",chr); split(a[2],b,"\\.\\."); start=b[1]-1; end=b[2]; print chr"\t"start"\t"end"\t"$2"\t"$3}' /scratch/negishi/hasan128/data/clumping/range_clumping/DBP_UKB_KEATON_DBP_MAF_harmonized_range_clumped.clumped.ranges > /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.bed

sort -k1,1 -k2,2n /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.bed > /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.sorted.bed

sort -k1,1 -k2,2n /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.bed > /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed

#shared loci between cross and large ancestry

bedtools intersect -a /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.sorted.bed -b /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed -wa -wb > /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_vs_large_overlap.tsv


#cross-only loci
bedtools intersect -a /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.sorted.bed -b /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed -v > /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_only_loci.bed


#large-only loci
bedtools intersect -a /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed -b /scratch/negishi/hasan128/data/clumping/intersect/DBP_cross_regions.sorted.bed -v > /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_only_loci.bed


```

























```bash
# DBP_keaton_first_afr_only_META_1.tbl - 
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/DBP/UKB_KEATON_DBP_MAF_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/DBP/mvp_dbp_maf_harmonized.tsv
# --> Input File 3 : /scratch/negishi/hasan128/data/harmonized/DBP/UKB_AFR_MAF_DBP_harmonized.tsv


DBP_keaton_mvp_afr_only_META_1.tbl
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/DBP/UKB_KEATON_DBP_MAF_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/DBP/mvp_dbp_maf_harmonized.tsv


DBP_keaton_ukb_afr__META_1.tbl
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/DBP/UKB_KEATON_DBP_MAF_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/DBP/UKB_AFR_MAF_DBP_harmonized.tsv






# SBP

SBP_afr_first_with_keaton_only_META_1.tbl
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/SBP/ukb_keaton_maf_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/SBP/MVP_afr_maf_harmonized.tsv
# --> Input File 3 : /scratch/negishi/hasan128/data/harmonized/SBP/UKB_Afr_maf_harmonized.tsv


SBP_keaton_MVP_afr_META_1.tbl.info
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/SBP/ukb_keaton_maf_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/SBP/MVP_afr_maf_harmonized.tsv



SBP_keaton_UKB_afr_META_1.tbl
# Input for this meta-analysis was stored in the files:
# --> Input File 1 : /scratch/negishi/hasan128/data/harmonized/SBP/ukb_keaton_maf_harmonized.tsv
# --> Input File 2 : /scratch/negishi/hasan128/data/harmonized/SBP/UKB_Afr_maf_harmonized.tsv


```


