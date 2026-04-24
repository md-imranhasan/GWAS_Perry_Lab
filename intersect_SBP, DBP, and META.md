# Intersect All

like our files are:
``` bash
A1C_cross_meta1_clumped_sorted.bed          DBP_UKB_KEATON_DBP_MAF_harmonized_sorted.bed  SBP_cross_meta_nokeaton1_sorted.bed
A1C_UKB_EUR_filtered_harmonized_sorted.bed  eGFR_cross_meta1_clumped_sorted.bed           SBP_ukb_keaton_maf_harmonized_sorted.bed
AF_cross_meta1_clumped_sorted.bed           eGFR_eGFR_gwas_eur_harmonized_sorted.bed      STROKE_cross_meta1_clumped_sorted.bed
AF_eur_gwas_AF_MAF_harmonized_sorted.bed    HDL_cross_meta1_clumped_sorted.bed            T2D_cross_meta1_clumped_sorted.bed
BMI_cross_meta1_clumped_sorted.bed          HDL_EUR_HDL_maf_harmonized_sorted.bed         T2D_DIAMANTE-EUR_maf_harmonized_sorted.bed
BMI_giant_maf_harmonized_sorted.bed         HF_cross_meta1_clumped_sorted.bed             TG_cross_meta1_clumped_sorted.bed
CRF_cross_meta1_clumped_sorted.bed          HTN_cross_meta1_clumped_sorted.bed            TG_TG_EUR_MAF_harmonized_sorted.bed
CRF_MVP_EUR_CRF_MAF_harmonized_sorted.bed   HTN_ukb_eur_maf_harmonized_sorted.bed         TROPO_cross_meta1_clumped_sorted.bed
DBP_cross_meta1_clumped_sorted.bed                                             WC_cross_meta1_clumped_sorted.bed
DBP_cross_meta_nokeaton1_sorted.bed         SBP_cross_meta1_clumped_sorted.bed            WC_Giant_maf_eur_harmonized_sorted.bed
```

``` bash
mkdir -p intersect && for trait in A1C AF BMI CRF DBP eGFR HDL HF HTN SBP STROKE T2D TG TROPO WC; do meta="${trait}_cross_meta1_clumped_sorted.bed"; gwas=$(ls ${trait}_*_harmonized_sorted.bed 2>/dev/null | head -n 1); if [[ -f "$meta" && -n "$gwas" ]]; then echo "Processing $trait..."; awk '$1 ~ /^[0-9]+$/ && $1 <= 22' "$meta" > tmp_m.bed; awk '$1 ~ /^[0-9]+$/ && $1 <= 22' "$gwas" > tmp_g.bed; bedtools intersect -a tmp_m.bed -b tmp_g.bed -wa -c > "intersect/${trait}_Meta_overlaps_GWAS_24_4.tsv"; bedtools intersect -a tmp_g.bed -b tmp_m.bed -wa -c > "intersect/${trait}_GWAS_overlaps_Meta_24_4.tsv"; bedtools intersect -a tmp_m.bed -b tmp_g.bed -v > "intersect/${trait}_Meta_Unique_Loci_24_4.bed"; bedtools intersect -a tmp_g.bed -b tmp_m.bed -v > "intersect/${trait}_GWAS_Unique_Loci_24_4.bed"; rm tmp_m.bed tmp_g.bed; fi; done && echo "All done!"
```

``` bash
awk 'NR>1 {gsub(/\r/,""); print $1}' sbp_keaton_meta_ukb_status_with_header.tsv > target_snps.txt
```

``` bash
awk '
BEGIN { OFS="\t" }

# Scrub invisible Windows carriage returns from every line
{ gsub(/\r/, ""); }

# 1. Save target SNPs
NR==FNR { snp[$1]=1; order[++n]=$1; next }

# 2. Extract data if the SNP matches
{
    # Meta file (RSID is col 1)
    if (FILENAME == ARGV[2] && $1 in snp) { 
        m_p[$1]=$10; m_ea[$1]=$2; m_nea[$1]=$3 
    }
    # Harmonized files (RSID is col 3)
    else if ($3 in snp) {
        if (FILENAME == ARGV[3]) { mvp_p[$3]=$9; mvp_ea[$3]=$4; mvp_nea[$3]=$5 }
        if (FILENAME == ARGV[4]) { ukb_p[$3]=$9; ukb_ea[$3]=$4; ukb_nea[$3]=$5 }
        if (FILENAME == ARGV[5]) { fin_p[$3]=$9; fin_ea[$3]=$4; fin_nea[$3]=$5 }
        if (FILENAME == ARGV[6]) { kea_p[$3]=$9; kea_ea[$3]=$4; kea_nea[$3]=$5 }
    }
}

# 3. Print the final combined table
END {
    # Header
    print "SNP", "Meta_Status","Meta_P","Meta_EA","Meta_NEA", "MVP_Status","MVP_P","MVP_EA","MVP_NEA", "UKB_Status","UKB_P","UKB_EA","UKB_NEA", "FIN_Status","FIN_P","FIN_EA","FIN_NEA", "KEA_Status","KEA_P","KEA_EA","KEA_NEA"
    
    # Rows
    for(i=1; i<=n; i++) {
        s = order[i]
        print s, \
            (s in m_p?"Present":"Missing"), (s in m_p?m_p[s]:"NA"), (s in m_ea?m_ea[s]:"NA"), (s in m_nea?m_nea[s]:"NA"), \
            (s in mvp_p?"Present":"Missing"), (s in mvp_p?mvp_p[s]:"NA"), (s in mvp_ea?mvp_ea[s]:"NA"), (s in mvp_nea?mvp_nea[s]:"NA"), \
            (s in ukb_p?"Present":"Missing"), (s in ukb_p?ukb_p[s]:"NA"), (s in ukb_ea?ukb_ea[s]:"NA"), (s in ukb_nea?ukb_nea[s]:"NA"), \
            (s in fin_p?"Present":"Missing"), (s in fin_p?fin_p[s]:"NA"), (s in fin_ea?fin_ea[s]:"NA"), (s in fin_nea?fin_nea[s]:"NA"), \
            (s in kea_p?"Present":"Missing"), (s in kea_p?kea_p[s]:"NA"), (s in kea_ea?kea_ea[s]:"NA"), (s in kea_nea?kea_nea[s]:"NA")
    }
}' \
target_snps.txt \
/scratch/negishi/hasan128/data/manhatton/SBP_ALL_META_1_with_chr_pos.tbl \
/scratch/negishi/hasan128/data/harmonized/SBP/MVP_afr_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/UKB_Afr_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/Finngen_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/ukb_keaton_maf_harmonized.tsv \
> SBP_meta_4studies_status.tsv
```




``` bash
hasan128@a204.negishi:[pvalue] $ head SBP_meta_4studies_status.tsv
SNP     Meta_Status     Meta_P  Meta_EA Meta_NEA        MVP_Status      MVP_P   MVP_EA  MVP_NEA UKB_Status      UKB_P   UKB_EA  UKB_NEA FIN_Status      FIN_P   FIN_EA  FIN_NEA KEA_Status      KEA_P   KEA_EA  KEA_NEA
rs10018970      Present 0.2912  a       g       Present 0.3226  A       G       Present 0.931923        A       G       Present 0.2802  A       G       Present 4.243e-09       A       G
rs10054208      Present 0.1013  t       c       Present 0.5257  T       C       Present 0.366944        T       C       Present 0.504052        T       C       Present 2.724e-08       T       C
rs10066799      Present 0.01548 t       g       Present 0.08963 T       G       Present 0.185952        T       G       Present 0.656695        T       G       Present 7.962e-12       T       G
rs10072115      Present 0.5844  a       c       Present 0.8975  C       A       Present 0.42924 C       A       Present 0.867375        C       A       Present 4.008e-08       C       A
rs1009017       Present 0.05024 t       c       Present 0.03556 C       T       Present 0.162443        C       T       Present 0.979861        C       T       Present 6.637e-14       C       T
rs10172510      Present 0.01633 a       g       Present 0.2503  A       G       Present 0.382208        A       G       Present 0.11199 A       G       Present 2.849e-08       A       G
rs10183431      Present 0.3461  t       c       Present 0.516   C       T       Present 0.740798        C       T       Present 0.825876        C       T       Present 9.619e-09       C       T
rs10233127      Present 0.1719  a       t       Present 0.8162  A       T       Present 0.63387 A       T       Present 0.187865        A       T       Present 2.82e-13        A       T
rs1030009       Present 0.1543  t       c       Present 0.3039  T       C       Present 0.18281 T       C       Present 0.828111        T       C       Present 1.047e-09       T       C

```





# DBP

``` bash
awk '{print $4}' DBP_large_only_loci_15_4.bed > dbp_unique_snps.txt
```

``` bash
awk '
BEGIN { OFS="\t" }

# Scrub invisible Windows carriage returns from every line
{ gsub(/\r/, ""); }

# 1. Save target SNPs
NR==FNR { snp[$1]=1; order[++n]=$1; next }

# 2. Extract data if the SNP matches
{
    # Meta file (RSID is col 1)
    if (FILENAME == ARGV[2] && $1 in snp) { 
        m_p[$1]=$10; m_ea[$1]=$2; m_nea[$1]=$3 
    }
    # Harmonized files (RSID is col 3)
    else if ($3 in snp) {
        if (FILENAME == ARGV[3]) { mvp_p[$3]=$9; mvp_ea[$3]=$4; mvp_nea[$3]=$5 }
        if (FILENAME == ARGV[4]) { ukb_p[$3]=$9; ukb_ea[$3]=$4; ukb_nea[$3]=$5 }
        if (FILENAME == ARGV[5]) { fin_p[$3]=$9; fin_ea[$3]=$4; fin_nea[$3]=$5 }
        if (FILENAME == ARGV[6]) { kea_p[$3]=$9; kea_ea[$3]=$4; kea_nea[$3]=$5 }
    }
}

# 3. Print the final combined table
END {
    # Header
    print "SNP", "Meta_Status","Meta_P","Meta_EA","Meta_NEA", "MVP_Status","MVP_P","MVP_EA","MVP_NEA", "UKB_Status","UKB_P","UKB_EA","UKB_NEA", "FIN_Status","FIN_P","FIN_EA","FIN_NEA", "KEA_Status","KEA_P","KEA_EA","KEA_NEA"
    
    # Rows
    for(i=1; i<=n; i++) {
        s = order[i]
        print s, \
            (s in m_p?"Present":"Missing"), (s in m_p?m_p[s]:"NA"), (s in m_ea?m_ea[s]:"NA"), (s in m_nea?m_nea[s]:"NA"), \
            (s in mvp_p?"Present":"Missing"), (s in mvp_p?mvp_p[s]:"NA"), (s in mvp_ea?mvp_ea[s]:"NA"), (s in mvp_nea?mvp_nea[s]:"NA"), \
            (s in ukb_p?"Present":"Missing"), (s in ukb_p?ukb_p[s]:"NA"), (s in ukb_ea?ukb_ea[s]:"NA"), (s in ukb_nea?ukb_nea[s]:"NA"), \
            (s in fin_p?"Present":"Missing"), (s in fin_p?fin_p[s]:"NA"), (s in fin_ea?fin_ea[s]:"NA"), (s in fin_nea?fin_nea[s]:"NA"), \
            (s in kea_p?"Present":"Missing"), (s in kea_p?kea_p[s]:"NA"), (s in kea_ea?kea_ea[s]:"NA"), (s in kea_nea?kea_nea[s]:"NA")
    }
}' \
dbp_unique_snps.txt \
/scratch/negishi/hasan128/data/manhatton/DBP_ALL_META_1_with_chr_pos.tbl \
/scratch/negishi/hasan128/data/harmonized/DBP/mvp_dbp_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/DBP/UKB_AFR_MAF_DBP_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/DBP/FINNGEN_DBP_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/DBP/UKB_KEATON_DBP_MAF_harmonized.tsv \
> DBP_meta_4studies_status.tsv

```

```bash
hasan128@a204.negishi:[pvalue] $ head DBP_meta_4studies_status.tsv
SNP     Meta_Status     Meta_P  Meta_EA Meta_NEA        MVP_Status      MVP_P   MVP_EA  MVP_NEA UKB_Status      UKB_P   UKB_EA  UKB_NEA FIN_Status      FIN_P   FIN_EA  FIN_NEA KEA_Status      KEA_P   KEA_EA  KEA_NEA
rs3013093       Present 4.923e-06       t       c       Present 0.000528        C       T       Present 0.5124  C       T       Present 0.1078  C       T       Present 1.362e-08       C       T
rs2320590       Present 0.03276 t       c       Present 0.7816  T       C       Present 0.5732  T       C       Present 0.2574  T       C       Present 1.384e-08       T       C
rs2807337       Present 0.139   t       c       Present 0.4873  C       T       Present 0.8137  C       T       Present 0.9988  C       T       Present 2.12e-08        C       T
rs10889711      Present 0.002152        t       c       Present 0.7575  C       T       Present 0.4712  C       T       Present 0.03324 C       T       Present 6.566e-09       C       T
rs2275902       Present 0.001028        c       g       Present 0.05095 C       G       Present 0.1433  C       G       Present 0.2902  C       G       Present 3.486e-08       C       G
rs565522        Present 0.03227 t       c       Present 0.1514  C       T       Present 0.3976  C       T       Present 0.6569  C       T       Present 1.739e-08       C       T
rs6704339       Present 0.003049        a       g       Present 0.272   G       A       Present 0.6564  G       A       Present 0.09687 G       A       Present 1.444e-08       G       A
rs12078697      Present 0.0507  c       g       Present 0.131   C       G       Present 0.148   C       G       Present 0.007267        C       G       Present 9.028e-09       C       G
rs6669446       Present 0.008242        t       c       Present 0.01773 C       T       Present 0.712   C       T       Present 0.9124  C       T       Present 4.288e-09       C       T

```



# with Meta
```bash
# 1. Convert the Meta .tbl file into a proper BED format
awk 'BEGIN {OFS="\t"} 
     NR>1 && $12!="NA" && $12!="" && $13!="NA" && $13!="" {
         print $12, $13-1, $13, $1, $10
     }' /scratch/negishi/hasan128/data/manhatton/DBP_ALL_META_1_with_chr_pos.tbl > DBP_Meta_Unsorted.bed

# 2. Sort the newly created Meta BED file
sort -k1,1V -k2,2n DBP_Meta_Unsorted.bed > DBP_Meta_Sorted.bed

# 3. Intersection 1: A = Meta, B = Large Keaton (Counts Keaton loci overlapping each Meta SNP)
bedtools intersect \
  -a DBP_Meta_Sorted.bed \
  -b /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed \
  -wa -c -sorted > Meta_base_overlap_Keaton.tsv

# 4. Intersection 2: A = Large Keaton, B = Meta (Counts Meta SNPs overlapping each Keaton locus)
bedtools intersect \
  -a /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed \
  -b DBP_Meta_Sorted.bed \
  -wa -c -sorted > Keaton_base_overlap_Meta.tsv

# Optional: Clean up the unsorted intermediate file
rm DBP_Meta_Unsorted.bed

```





