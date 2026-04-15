hasan128@login01.negishi:[pvalue] $ echo "SBP Keaton total:" $(wc -l < /scratch/negishi/hasan128/data/clumping/intersect/SBP_large_regions.sorted.bed)
SBP Keaton total: 1820
hasan128@login01.negishi:[pvalue] $ echo "SBP Meta total:" $(wc -l < /scratch/negishi/hasan128/data/clumping/intersect/SBP_keaton_UKB_afr_clumped.sorted.bed)
SBP Meta total: 1262
hasan128@login01.negishi:[pvalue] $ echo "SBP Unique Keaton:" $(wc -l < SBP_large_only_loci.bed)
-bash: SBP_large_only_loci.bed: No such file or directory
SBP Unique Keaton:
hasan128@login01.negishi:[pvalue] $ echo "SBP Unique Keaton:" $(wc -l < SBP_large_only_loci_15_4.bed)
SBP Unique Keaton: 309
hasan128@login01.negishi:[pvalue] $ echo "SBP Unique Meta:" $(wc -l < SBP_meta_only_loci_15_4.bed)
SBP Unique Meta: 10
hasan128@login01.negishi:[pvalue] $ echo "DBP Keaton total:" $(wc -l < /scratch/negishi/hasan128/data/clumping/intersect/DBP_large_regions.sorted.bed)
DBP Keaton total: 1809
hasan128@login01.negishi:[pvalue] $ echo "DBP Meta total:" $(wc -l < /scratch/negishi/hasan128/data/clumping/intersect/DBP_keaton_ukb_afr__clumped.sorted.bed)
DBP Meta total: 1930
hasan128@login01.negishi:[pvalue] $ echo "DBP Unique Keaton:" $(wc -l < DBP_large_only_loci_15_4.bed)
DBP Unique Keaton: 157
hasan128@login01.negishi:[pvalue] $ echo "DBP Unique Meta:" $(wc -l < DBP_meta_only_loci_15_4.bed)
DBP Unique Meta: 24
