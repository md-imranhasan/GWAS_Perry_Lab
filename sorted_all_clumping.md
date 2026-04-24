``` bash
hasan128@a276.negishi:[ALL_clumping] $ ls
A1C_cross_meta1_clumped.clumped                   eGFR_cross_meta1_clumped.clumped              SBP_ukb_keaton_maf_harmonized.log
A1C_cross_meta1_clumped.clumped.ranges            eGFR_cross_meta1_clumped.clumped.ranges       sorted
A1C_cross_meta1_clumped.log                       eGFR_cross_meta1_clumped.log                  STROKE_cross_meta1_clumped.clumped
A1C_UKB_EUR_filtered_harmonized.clumped           eGFR_eGFR_gwas_eur_harmonized.clumped         STROKE_cross_meta1_clumped.clumped.ranges
A1C_UKB_EUR_filtered_harmonized.clumped.ranges    eGFR_eGFR_gwas_eur_harmonized.clumped.ranges  STROKE_cross_meta1_clumped.log
A1C_UKB_EUR_filtered_harmonized.log               eGFR_eGFR_gwas_eur_harmonized.log             STROKE_STROKE_EUR_MVP_harmonized.log
AF_cross_meta1_clumped.clumped                    HDL_cross_meta1_clumped.clumped               T2D_cross_meta1_clumped.clumped
AF_cross_meta1_clumped.clumped.ranges             HDL_cross_meta1_clumped.clumped.ranges        T2D_cross_meta1_clumped.clumped.ranges
AF_cross_meta1_clumped.log                        HDL_cross_meta1_clumped.log                   T2D_cross_meta1_clumped.log
BMI_cross_meta1_clumped.clumped                   HDL_EUR_HDL_maf_harmonized.clumped            T2D_DIAMANTE-EUR_maf_harmonized.clumped
BMI_cross_meta1_clumped.clumped.ranges            HDL_EUR_HDL_maf_harmonized.clumped.ranges     T2D_DIAMANTE-EUR_maf_harmonized.clumped.ranges
BMI_cross_meta1_clumped.log                       HDL_EUR_HDL_maf_harmonized.log                T2D_DIAMANTE-EUR_maf_harmonized.log
BMI_giant_maf_harmonized.clumped                  HF_cross_meta1_clumped.clumped                TG_cross_meta1_clumped.clumped
BMI_giant_maf_harmonized.clumped.ranges           HF_cross_meta1_clumped.clumped.ranges         TG_cross_meta1_clumped.clumped.ranges
BMI_giant_maf_harmonized.log                      HF_cross_meta1_clumped.log                    TG_cross_meta1_clumped.log
CRF_cross_meta1_clumped.clumped                   HF_mvp_eur_hf_maf_harmonized.log              TG_TG_EUR_MAF_harmonized.clumped
CRF_cross_meta1_clumped.clumped.ranges            HTN_cross_meta1_clumped.clumped               TG_TG_EUR_MAF_harmonized.clumped.ranges
CRF_cross_meta1_clumped.log                       HTN_cross_meta1_clumped.clumped.ranges        TG_TG_EUR_MAF_harmonized.log
CRF_MVP_EUR_CRF_MAF_harmonized.clumped            HTN_cross_meta1_clumped.log                   TROPO_cross_meta1_clumped.clumped
CRF_MVP_EUR_CRF_MAF_harmonized.clumped.ranges     HTN_ukb_eur_maf_harmonized.clumped            TROPO_cross_meta1_clumped.clumped.ranges
CRF_MVP_EUR_CRF_MAF_harmonized.log                HTN_ukb_eur_maf_harmonized.clumped.ranges     TROPO_cross_meta1_clumped.log
DBP_cross_meta1_clumped.clumped                   HTN_ukb_eur_maf_harmonized.log                TROPO_mvp_eur_maf_harmonized.log
DBP_cross_meta1_clumped.clumped.ranges            SBP_cross_meta1_clumped.clumped               WC_cross_meta1_clumped.clumped
DBP_cross_meta1_clumped.log                       SBP_cross_meta1_clumped.clumped.ranges        WC_cross_meta1_clumped.clumped.ranges
DBP_cross_meta_nokeaton1.clumped                  SBP_cross_meta1_clumped.log                   WC_cross_meta1_clumped.log
DBP_cross_meta_nokeaton1.clumped.ranges           SBP_cross_meta_nokeaton1.clumped              WC_Giant_maf_eur_harmonized.clumped
DBP_cross_meta_nokeaton1.log                      SBP_cross_meta_nokeaton1.clumped.ranges       WC_Giant_maf_eur_harmonized.clumped.ranges
DBP_UKB_KEATON_DBP_MAF_harmonized.clumped         SBP_cross_meta_nokeaton1.log                  WC_Giant_maf_eur_harmonized.log
DBP_UKB_KEATON_DBP_MAF_harmonized.clumped.ranges  SBP_ukb_keaton_maf_harmonized.clumped
DBP_UKB_KEATON_DBP_MAF_harmonized.log             SBP_ukb_keaton_maf_harmonized.clumped.ranges
hasan128@a276.negishi:[ALL_clumping] $ pwd
/depot/ppaschou/data/NEW_CKM/intersect/ALL_clumping

```

``` bash
hasan128@a276.negishi:[ALL_clumping] $ for file in *.clumped.ranges; do echo "Processing $file..."; awk 'BEGIN {OFS="\t"} NR>1 { gsub(/chr/, "", $5); split($5, coords, /:|\.\./); print coords[1], coords[2], coords[3], $2 }' "$file" | sort -k1,1V -k2,2n > "sorted/${file%.clumped.ranges}_sorted.bed"; done && echo "Done!"
```





