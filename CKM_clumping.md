```bash
 plink --bfile /depot/ppaschou/data/NEW_CKM/qc/g1000_eur --clump WC_EUR_META_1.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 1e-5 --clump-r2 0.1 --clump-kb 3000 --out WC_EUR_clumped

```
```bash
plink --bfile /depot/ppaschou/data/NEW_CKM/qc/g1000_eur_afr_autosome_merged.reid --clump A1C_ALL_META_1.tbl --clump-snp-field MarkerName --clump-field P-value --clump-p1 1e-5 --clump-r2 0.1 --clump-kb 3000 --out A1C_ALL_clumped
```
