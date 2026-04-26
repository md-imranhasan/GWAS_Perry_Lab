# all_15_traits_cross_ancestry_rg.log
``` bash
*********************************************************************
* LD Score Regression (LDSC)
* Version 1.0.1
* (C) 2014-2019 Brendan Bulik-Sullivan and Hilary Finucane
* Broad Institute of MIT and Harvard / MIT Department of Mathematics
* GNU General Public License v3
*********************************************************************
Call:
./ldsc.py \
--ref-ld-chr /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/ \
--out all_15_traits_cross_ancestry_rg \
--rg A1C_ALL_munged.sumstats.gz,AF_ALL_munged.sumstats.gz,BMI_ALL_munged.sumstats.gz,CRF_ALL_munged.sumstats.gz,DBP_ALL_munged.sumstats.gz,eGFR_ALL_munged.sumstats.gz,HDL_ALL_munged.sumstats.gz,HF_ALL_munged.sumstats.gz,HTN_ALL_munged.sumstats.gz,SBP_ALL_munged.sumstats.gz,STROKE_ALL_munged.sumstats.gz,T2D_ALL_munged.sumstats.gz,TG_ALL_munged.sumstats.gz,TROPO_ALL_munged.sumstats.gz,WC_ALL_munged.sumstats.gz \
--w-ld-chr /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/

Beginning analysis at Fri Apr  3 00:06:16 2026
Reading summary statistics from A1C_ALL_munged.sumstats.gz ...
Read summary statistics for 1215257 SNPs.
Reading reference panel LD Score from /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/[1-22] ...
Read reference panel LD Scores for 49141563 SNPs.
Removing partitioned LD Scores with zero variance.
Reading regression weight LD Score from /depot/ppaschou/data/ukb_mary/crm/eur+afr_LD/[1-22] ...
Read regression weight LD Scores for 49141563 SNPs.
After merging with reference panel LD, 1205092 SNPs remain.
After merging with regression SNP LD, 1205092 SNPs remain.
Computing rg for phenotype 2/15
Reading summary statistics from AF_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1204183 SNPs with valid alleles.

Heritability of phenotype 1
---------------------------
Total Observed scale h2: 0.1037 (0.0109)
Lambda GC: 2.1328
Mean Chi^2: 3.5078
Intercept: 1.8635 (0.0955)
Ratio: 0.3443 (0.0381)

Heritability of phenotype 2/15
------------------------------
Total Observed scale h2: 0.119 (0.012)
Lambda GC: 1.8566
Mean Chi^2: 2.6857
Intercept: 1.5238 (0.0543)
Ratio: 0.3107 (0.0322)

Genetic Covariance
------------------
Total Observed scale gencov: 0.01 (0.0026)
Mean z1*z2: 0.1876
Intercept: 0.0826 (0.0159)

Genetic Correlation
-------------------
Genetic Correlation: 0.0901 (0.0241)
Z-score: 3.7346
P: 0.0002

Computing rg for phenotype 3/15
Reading summary statistics from BMI_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203818 SNPs with valid alleles.

Heritability of phenotype 3/15
------------------------------
Total Observed scale h2: 0.2365 (0.0089)
Lambda GC: 4.8151
Mean Chi^2: 7.9421
Intercept: 2.4385 (0.0774)
Ratio: 0.2072 (0.0111)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0453 (0.0038)
Mean z1*z2: 1.1648
Intercept: 0.2859 (0.0312)

Genetic Correlation
-------------------
Genetic Correlation: 0.2926 (0.0274)
Z-score: 10.6591
P: 1.582e-26

Computing rg for phenotype 4/15
Reading summary statistics from CRF_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203232 SNPs with valid alleles.

Heritability of phenotype 4/15
------------------------------
Total Observed scale h2: 0.059 (0.005)
Lambda GC: 1.4423
Mean Chi^2: 1.5425
Intercept: 1.2431 (0.018)
Ratio: 0.448 (0.0332)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0272 (0.0027)
Mean z1*z2: 0.4426
Intercept: 0.2064 (0.0152)

Genetic Correlation
-------------------
Genetic Correlation: 0.353 (0.0312)
Z-score: 11.3013
P: 1.2927e-29

Computing rg for phenotype 5/15
Reading summary statistics from DBP_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203875 SNPs with valid alleles.

Heritability of phenotype 5/15
------------------------------
Total Observed scale h2: 0.0209 (0.0014)
Lambda GC: 1.4962
Mean Chi^2: 1.6816
Intercept: 1.2122 (0.018)
Ratio: 0.3113 (0.0264)

Genetic Covariance
------------------
Total Observed scale gencov: 0.002 (0.0015)
Mean z1*z2: 0.0363
Intercept: 0.0091 (0.0129)

Genetic Correlation
-------------------
Genetic Correlation: 0.0431 (0.0333)
Z-score: 1.2936
P: 0.1958

Computing rg for phenotype 6/15
Reading summary statistics from eGFR_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1204187 SNPs with valid alleles.

Heritability of phenotype 6/15
------------------------------
Total Observed scale h2: 0.069 (0.0054)
Lambda GC: 2.1069
Mean Chi^2: 4.2249
Intercept: 1.7253 (0.1047)
Ratio: 0.2249 (0.0325)

Genetic Covariance
------------------
Total Observed scale gencov: -0.0003 (0.0024)
Mean z1*z2: -0.0562
Intercept: -0.0597 (0.0242)

Genetic Correlation
-------------------
Genetic Correlation: -0.0041 (0.0287)
Z-score: -0.1418
P: 0.8873

Computing rg for phenotype 7/15
Reading summary statistics from HDL_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203554 SNPs with valid alleles.

Heritability of phenotype 7/15
------------------------------
Total Observed scale h2: 0.1543 (0.0144)
Lambda GC: 2.8971
Mean Chi^2: 6.0066
Intercept: 2.8855 (0.3755)
Ratio: 0.3766 (0.075)

Genetic Covariance
------------------
Total Observed scale gencov: -0.0335 (0.0037)
Mean z1*z2: -0.7935
Intercept: -0.2403 (0.0312)

Genetic Correlation
-------------------
Genetic Correlation: -0.2654 (0.0332)
Z-score: -8.0057
P: 1.1874e-15

Computing rg for phenotype 8/15
Reading summary statistics from HF_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203806 SNPs with valid alleles.

Heritability of phenotype 8/15
------------------------------
Total Observed scale h2: 0.0409 (0.0029)
Lambda GC: 1.3306
Mean Chi^2: 1.4071
Intercept: 1.1786 (0.0111)
Ratio: 0.4389 (0.0274)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0186 (0.0021)
Mean z1*z2: 0.2476
Intercept: 0.0789 (0.0111)

Genetic Correlation
-------------------
Genetic Correlation: 0.2895 (0.0346)
Z-score: 8.3737
P: 5.5816e-17

Computing rg for phenotype 9/15
Reading summary statistics from HTN_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1204109 SNPs with valid alleles.

Heritability of phenotype 9/15
------------------------------
Total Observed scale h2: 0.1185 (0.0063)
Lambda GC: 3.1973
Mean Chi^2: 4.2881
Intercept: 2.3274 (0.05)
Ratio: 0.4037 (0.0152)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0333 (0.0031)
Mean z1*z2: 0.8173
Intercept: 0.3035 (0.0214)

Genetic Correlation
-------------------
Genetic Correlation: 0.3017 (0.0293)
Z-score: 10.2892
P: 7.8865e-25

Computing rg for phenotype 10/15
Reading summary statistics from SBP_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1203840 SNPs with valid alleles.

Heritability of phenotype 10/15
-------------------------------
Total Observed scale h2: 0.0154 (0.0014)
Lambda GC: 1.4245
Mean Chi^2: 1.5401
Intercept: 1.1976 (0.0143)
Ratio: 0.3658 (0.0264)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0081 (0.0017)
Mean z1*z2: 0.1917
Intercept: 0.0455 (0.0143)

Genetic Correlation
-------------------
Genetic Correlation: 0.2032 (0.0358)
Z-score: 5.6745
P: 1.3909e-08

Computing rg for phenotype 11/15
Reading summary statistics from STROKE_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1202955 SNPs with valid alleles.

Heritability of phenotype 11/15
-------------------------------
Total Observed scale h2: 0.0242 (0.0021)
Lambda GC: 1.2464
Mean Chi^2: 1.2965
Intercept: 1.14 (0.009)
Ratio: 0.4722 (0.0304)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0129 (0.002)
Mean z1*z2: 0.1775
Intercept: 0.0529 (0.0101)

Genetic Correlation
-------------------
Genetic Correlation: 0.2618 (0.0404)
Z-score: 6.4886
P: 8.6618e-11

Computing rg for phenotype 12/15
Reading summary statistics from T2D_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1204807 SNPs with valid alleles.

Heritability of phenotype 12/15
-------------------------------
Total Observed scale h2: 0.1433 (0.0078)
Lambda GC: 2.9478
Mean Chi^2: 4.2272
Intercept: 2.0166 (0.0956)
Ratio: 0.315 (0.0296)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0897 (0.0057)
Mean z1*z2: 2.1594
Intercept: 0.8001 (0.0667)

Genetic Correlation
-------------------
Genetic Correlation: 0.7298 (0.0248)
Z-score: 29.4372
P: 1.8382e-190

Computing rg for phenotype 13/15
Reading summary statistics from TG_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1202692 SNPs with valid alleles.

Heritability of phenotype 13/15
-------------------------------
Total Observed scale h2: 0.1929 (0.0248)
Lambda GC: 1.959
Mean Chi^2: 3.7867
Intercept: 1.6145 (0.135)
Ratio: 0.2205 (0.0485)

Genetic Covariance
------------------
Total Observed scale gencov: 0.04 (0.0049)
Mean z1*z2: 0.7416
Intercept: 0.2611 (0.0249)

Genetic Correlation
-------------------
Genetic Correlation: 0.2864 (0.0496)
Z-score: 5.7713
P: 7.8645e-09

Computing rg for phenotype 14/15
Reading summary statistics from TROPO_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1204075 SNPs with valid alleles.

Heritability of phenotype 14/15
-------------------------------
Total Observed scale h2: 0.0291 (0.0044)
Lambda GC: 1.0833
Mean Chi^2: 1.1034
Intercept: 1.0187 (0.008)
Ratio: 0.1805 (0.0772)

Genetic Covariance
------------------
Total Observed scale gencov: 0.009 (0.0023)
Mean z1*z2: 0.0959
Intercept: 0.0327 (0.0092)

Genetic Correlation
-------------------
Genetic Correlation: 0.1648 (0.0438)
Z-score: 3.7627
P: 0.0002

Computing rg for phenotype 15/15
Reading summary statistics from WC_ALL_munged.sumstats.gz ...
Read summary statistics for 1217311 SNPs.
After merging with summary statistics, 1205092 SNPs remain.
1201532 SNPs with valid alleles.

Heritability of phenotype 15/15
-------------------------------
Total Observed scale h2: 0.1496 (0.0073)
Lambda GC: 1.9714
Mean Chi^2: 2.6341
Intercept: 1.2298 (0.0233)
Ratio: 0.1406 (0.0143)

Genetic Covariance
------------------
Total Observed scale gencov: 0.0439 (0.0033)
Mean z1*z2: 0.7509
Intercept: 0.2172 (0.0177)

Genetic Correlation
-------------------
Genetic Correlation: 0.3558 (0.0281)
Z-score: 12.6474
P: 1.1562e-36


Summary of Genetic Correlation Results
p1                             p2      rg      se        z            p  h2_obs  h2_obs_se  h2_int  h2_int_se  gcov_int  gcov_int_se
A1C_ALL_munged.sumstats.gz      AF_ALL_munged.sumstats.gz  0.0901  0.0241   3.7346   1.8803e-04  0.1190     0.0120  1.5238     0.0543    0.0826       0.0159
A1C_ALL_munged.sumstats.gz     BMI_ALL_munged.sumstats.gz  0.2926  0.0274  10.6591   1.5820e-26  0.2365     0.0089  2.4385     0.0774    0.2859       0.0312
A1C_ALL_munged.sumstats.gz     CRF_ALL_munged.sumstats.gz  0.3530  0.0312  11.3013   1.2927e-29  0.0590     0.0050  1.2431     0.0180    0.2064       0.0152
A1C_ALL_munged.sumstats.gz     DBP_ALL_munged.sumstats.gz  0.0431  0.0333   1.2936   1.9581e-01  0.0209     0.0014  1.2122     0.0180    0.0091       0.0129
A1C_ALL_munged.sumstats.gz    eGFR_ALL_munged.sumstats.gz -0.0041  0.0287  -0.1418   8.8726e-01  0.0690     0.0054  1.7253     0.1047   -0.0597       0.0242
A1C_ALL_munged.sumstats.gz     HDL_ALL_munged.sumstats.gz -0.2654  0.0332  -8.0057   1.1874e-15  0.1543     0.0144  2.8855     0.3755   -0.2403       0.0312
A1C_ALL_munged.sumstats.gz      HF_ALL_munged.sumstats.gz  0.2895  0.0346   8.3737   5.5816e-17  0.0409     0.0029  1.1786     0.0111    0.0789       0.0111
A1C_ALL_munged.sumstats.gz     HTN_ALL_munged.sumstats.gz  0.3017  0.0293  10.2892   7.8865e-25  0.1185     0.0063  2.3274     0.0500    0.3035       0.0214
A1C_ALL_munged.sumstats.gz     SBP_ALL_munged.sumstats.gz  0.2032  0.0358   5.6745   1.3909e-08  0.0154     0.0014  1.1976     0.0143    0.0455       0.0143
A1C_ALL_munged.sumstats.gz  STROKE_ALL_munged.sumstats.gz  0.2618  0.0404   6.4886   8.6618e-11  0.0242     0.0021  1.1400     0.0090    0.0529       0.0101
A1C_ALL_munged.sumstats.gz     T2D_ALL_munged.sumstats.gz  0.7298  0.0248  29.4372  1.8382e-190  0.1433     0.0078  2.0166     0.0956    0.8001       0.0667
A1C_ALL_munged.sumstats.gz      TG_ALL_munged.sumstats.gz  0.2864  0.0496   5.7713   7.8645e-09  0.1929     0.0248  1.6145     0.1350    0.2611       0.0249
A1C_ALL_munged.sumstats.gz   TROPO_ALL_munged.sumstats.gz  0.1648  0.0438   3.7627   1.6808e-04  0.0291     0.0044  1.0187     0.0080    0.0327       0.0092
A1C_ALL_munged.sumstats.gz      WC_ALL_munged.sumstats.gz  0.3558  0.0281  12.6474   1.1562e-36  0.1496     0.0073  1.2298     0.0233    0.2172       0.0177

Analysis finished at Fri Apr  3 00:10:03 2026
```
