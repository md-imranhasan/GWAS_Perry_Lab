/ldsc.py \
--out /scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/outputs/LDSC_BASELINE_20261015T055744Z_785034/dep/dep.baselineLD_v2_2 \
--h2 /scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/outputs/LDSC_BASELINE_20261015T055744Z_785034/dep/dep.registry_N.sumstats.gz \
--ref-ld-chr /scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD. \
--w-ld-chr /scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_weights_hm3_no_MHC/1000G_Phase3_weights_hm3_no_MHC/weights.hm3_noMHC. \
--overlap-annot  \
--print-coefficients  \
--frqfile-chr /scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_frq/1000G_Phase3_frq/1000G.EUR.QC. \
--print-delete-vals 




we download before , just its usefull

hasan128@login04.negishi:[ldsc] $ ls
'baselineLD_v2.1_annots 2'                           LDSCORE_1000G_Phase3_weights_hm3_no_MHC.tar.gz   LDSCORE_hapmap3_snps.tar                                      LDSCORE_LDSC_SEG_ldscores_Corces_ATAC_1000Gv3_ldscores.tar.gz      LDSCORE_LDSC_SEG_ldscores_ImmGen_1000Gv3_ldscores.tar.gz
 LDSCORE_1000G_Phase3_baselineLD_v2.2_ldscores.tar   LDSCORE_eur_ref_ld_chr.tar.gz                    LDSCORE_LDSC_SEG_ldscores_Cahoy_1000Gv3_ldscores.tar          LDSCORE_LDSC_SEG_ldscores_GTEx_brain_1000Gv3_ldscores.tar.gz       LDSCORE_LDSC_SEG_ldscores_Multi_tissue_gene_expr_1000Gv3_ldscores.tar.gz
 LDSCORE_1000G_Phase3_cell_type_groups.tar.gz        LDSCORE_eur_w_ld_chr.tar.gz                      LDSCORE_LDSC_SEG_ldscores_Cahoy_EAS_1000Gv3_ldscores.tar.gz   LDSCORE_LDSC_SEG_ldscores_GTEx_brain_EAS_1000Gv3_ldscores.tar.gz   reference.1000G.maf.0.005.txt
hasan128@login04.negishi:[ldsc] $ pwd
/depot/ppaschou/data/ldsc




hasan128@login04.negishi:[data] $ cd 1000GP_Phase3/
hasan128@login04.negishi:[1000GP_Phase3] $ ls
1000GP_Phase3_chr10.hap.gz     1000GP_Phase3_chr14.hap.gz     1000GP_Phase3_chr18.hap.gz     1000GP_Phase3_chr21.hap.gz     1000GP_Phase3_chr4.hap.gz     1000GP_Phase3_chr8.hap.gz           genetic_map_chr12_combined_b37.txt  genetic_map_chr1_combined_b37.txt   genetic_map_chr6_combined_b37.txt
1000GP_Phase3_chr10.legend.gz  1000GP_Phase3_chr14.legend.gz  1000GP_Phase3_chr18.legend.gz  1000GP_Phase3_chr21.legend.gz  1000GP_Phase3_chr4.legend.gz  1000GP_Phase3_chr8.legend.gz        genetic_map_chr13_combined_b37.txt  genetic_map_chr20_combined_b37.txt  genetic_map_chr7_combined_b37.txt
1000GP_Phase3_chr11.hap.gz     1000GP_Phase3_chr15.hap.gz     1000GP_Phase3_chr19.hap.gz     1000GP_Phase3_chr22.hap.gz     1000GP_Phase3_chr5.hap.gz     1000GP_Phase3_chr9.hap.gz           genetic_map_chr14_combined_b37.txt  genetic_map_chr21_combined_b37.txt  genetic_map_chr8_combined_b37.txt
1000GP_Phase3_chr11.legend.gz  1000GP_Phase3_chr15.legend.gz  1000GP_Phase3_chr19.legend.gz  1000GP_Phase3_chr22.legend.gz  1000GP_Phase3_chr5.legend.gz  1000GP_Phase3_chr9.legend.gz        genetic_map_chr15_combined_b37.txt  genetic_map_chr22_combined_b37.txt  genetic_map_chr9_combined_b37.txt
1000GP_Phase3_chr12.hap.gz     1000GP_Phase3_chr16.hap.gz     1000GP_Phase3_chr1.hap.gz      1000GP_Phase3_chr2.hap.gz      1000GP_Phase3_chr6.hap.gz     1000GP_Phase3.sample                genetic_map_chr16_combined_b37.txt  genetic_map_chr2_combined_b37.txt   ref_snps
1000GP_Phase3_chr12.legend.gz  1000GP_Phase3_chr16.legend.gz  1000GP_Phase3_chr1.legend.gz   1000GP_Phase3_chr2.legend.gz   1000GP_Phase3_chr6.legend.gz  beta@16.0.00000084045               genetic_map_chr17_combined_b37.txt  genetic_map_chr3_combined_b37.txt   tmp
1000GP_Phase3_chr13.hap.gz     1000GP_Phase3_chr17.hap.gz     1000GP_Phase3_chr20.hap.gz     1000GP_Phase3_chr3.hap.gz      1000GP_Phase3_chr7.hap.gz     genetic_map_chr10_combined_b37.txt  genetic_map_chr18_combined_b37.txt  genetic_map_chr4_combined_b37.txt
1000GP_Phase3_chr13.legend.gz  1000GP_Phase3_chr17.legend.gz  1000GP_Phase3_chr20.legend.gz  1000GP_Phase3_chr3.legend.gz   1000GP_Phase3_chr7.legend.gz  genetic_map_chr11_combined_b37.txt  genetic_map_chr19_combined_b37.txt  genetic_map_chr5_combined_b37.txt
hasan128@login04.negishi:[1000GP_Phase3] $ pwd
/depot/pdrineas/data/1000GP_Phase3






RESOURCE: 1000G_Phase3_baselineLD_v2.2_ldscores.tgz
PASS: 1000G_Phase3_baselineLD_v2.2_ldscores.tgz

RESOURCE: 1000G_Phase3_weights_hm3_no_MHC.tgz
PASS: 1000G_Phase3_weights_hm3_no_MHC.tgz

RESOURCE: 1000G_Phase3_frq.tgz
PASS: 1000G_Phase3_frq.tgz

RESOURCE: 1000G_Phase3_plinkfiles.tgz
Downloading: https://zenodo.org/records/10515792/files/1000G_Phase3_plinkfiles.tgz?download=1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  274M  100  274M    0     0  1665k      0  0:02:48  0:02:48 --:--:-- 1454k
PASS: 1000G_Phase3_plinkfiles.tgz

RESOURCE: hm3_no_MHC.list.txt
Downloading: https://zenodo.org/records/10515792/files/hm3_no_MHC.list.txt?download=1
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.8M  100 11.8M    0     0   582k      0  0:00:20  0:00:20 --:--:--  723k
PASS: hm3_no_MHC.list.txt

FINAL RECOVERY REPORT
{
  "status": "REFERENCE_RECOVERY_INCOMPLETE",
  "resources_passed": 5,
  "resources_requested": 6,
  "resources": [
    {
      "name": "1000G_Phase3_baselineLD_v2.2_ldscores.tgz",
      "expected_md5": "b261e0caf06a003e7522938e01b3d349",
      "status": "PASS",
      "source": "EXISTING_CHECKSUM_VERIFIED_DOWNLOAD",
      "archive": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/downloads/1000G_Phase3_baselineLD_v2.2_ldscores.tgz",
      "bytes": 675845447,
      "md5": "b261e0caf06a003e7522938e01b3d349",
      "publisher_checksum_verified": true,
      "extraction_status": "EXISTING_EXTRACTION",
      "extracted_directory": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores"
    },
    {
      "name": "1000G_Phase3_weights_hm3_no_MHC.tgz",
      "expected_md5": "a98ac0f089ee285177544a3e6e721ca3",
      "status": "PASS",
      "source": "EXISTING_CHECKSUM_VERIFIED_DOWNLOAD",
      "archive": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/downloads/1000G_Phase3_weights_hm3_no_MHC.tgz",
      "bytes": 12757654,
      "md5": "a98ac0f089ee285177544a3e6e721ca3",
      "publisher_checksum_verified": true,
      "extraction_status": "EXISTING_EXTRACTION",
      "extracted_directory": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_weights_hm3_no_MHC"
    },
    {
      "name": "1000G_Phase3_frq.tgz",
      "expected_md5": "ac29686ffd5b6378789857a522ebca77",
      "status": "PASS",
      "source": "EXISTING_CHECKSUM_VERIFIED_DOWNLOAD",
      "archive": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/downloads/1000G_Phase3_frq.tgz",
      "bytes": 85939688,
      "md5": "ac29686ffd5b6378789857a522ebca77",
      "publisher_checksum_verified": true,
      "extraction_status": "EXISTING_EXTRACTION",
      "extracted_directory": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_frq"
    },
    {
      "name": "1000G_Phase3_plinkfiles.tgz",
      "expected_md5": "a7773ab485827b533cb300c76356d76b",
      "status": "PASS",
      "source": "https://zenodo.org/records/10515792/files/1000G_Phase3_plinkfiles.tgz?download=1",
      "archive": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/downloads/1000G_Phase3_plinkfiles.tgz",
      "bytes": 288277344,
      "md5": "a7773ab485827b533cb300c76356d76b",
      "publisher_checksum_verified": true,
      "extraction_status": "EXTRACTED",
      "extracted_directory": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_plinkfiles"
    },
    {
      "name": "hm3_no_MHC.list.txt",
      "expected_md5": "65a34c68833eb4a764d0707b5505b508",
      "status": "PASS",
      "source": "https://zenodo.org/records/10515792/files/hm3_no_MHC.list.txt?download=1",
      "archive": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/downloads/hm3_no_MHC.list.txt",
      "bytes": 12395058,
      "md5": "65a34c68833eb4a764d0707b5505b508",
      "publisher_checksum_verified": true,
      "first_five_lines": [
        "rs3094315",
        "rs3131972",
        "rs3131969",
        "rs1048488",
        "rs3115850"
      ]
    },
    {
      "name": "SHARED_HAPMAP3_CHROMOSOME_LISTS",
      "archive": "/depot/ppaschou/data/ldsc/LDSCORE_hapmap3_snps.tar",
      "publisher_checksum_verified": false,
      "status": "FAILED",
      "error": "Unexpected SNP IDs: chromosome 1"
    }
  ],
  "shared_depot_files_modified": false,
  "singlebrain_environment_modified": false,
  "baseline_annotation_to_BIM_order_verified": false,
  "software_environment_installed": false,
  "models_run": false,
  "report_directory": "/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/reports/REFERENCE_RECOVERY_20261015T051447Z"
}

CHROMOSOME-1 FILE EXAMPLES
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD.1.annot.gz
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD.1.l2.M
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD.1.l2.M_5_50
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD.1.l2.ldscore.gz
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_baselineLD_v2.2_ldscores/baselineLD.1.log
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_frq/1000G_Phase3_frq/1000G.EUR.QC.1.frq
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_plinkfiles/1000G_EUR_Phase3_plink/1000G.EUR.QC.1.bed
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_plinkfiles/1000G_EUR_Phase3_plink/1000G.EUR.QC.1.bim
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_plinkfiles/1000G_EUR_Phase3_plink/1000G.EUR.QC.1.fam
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/1000G_Phase3_weights_hm3_no_MHC/1000G_Phase3_weights_hm3_no_MHC/weights.hm3_noMHC.1.l2.ldscore.gz
/scratch/negishi/hasan128/developmental_psychiatric_genomics/handoff/Developmental_Genomics_Start_v8/resources/LDSC_GRCh37/extracted/shared_hapmap3_snps/hapmap3_snps/hm.1.snp
