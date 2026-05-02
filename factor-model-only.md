


``` bash
nano run_M7_userGWAS.R
```

``` bash
# ============================================================
# Genomic SEM userGWAS using final M7 renal 6-factor model
# ============================================================

.libPaths(c("~/Rlibs_4.3.1", .libPaths()))

library(GenomicSEM)
library(data.table)

# =========================
# PATHS
# =========================

base_dir <- "/depot/ppaschou/data/NEW_CKM/intersect/ALL_clumping/manhattan/cross_munged/genomicsem_final_npkeaton_all15_4_30"

ldsc_file <- file.path(base_dir, "ldsc_out.RData")
snps_file <- file.path(base_dir, "SNPs_sumstats.RData")

out_dir <- file.path(base_dir, "M7_renal_6factor_userGWAS")
dir.create(out_dir, recursive = TRUE, showWarnings = FALSE)

# =========================
# LOAD LDSC OUTPUT
# =========================

if (!file.exists(ldsc_file)) {
  stop("Missing LDSC file: ", ldsc_file)
}

loaded_ldsc <- load(ldsc_file)

if ("ldsc_out" %in% loaded_ldsc) {
  cat("Loaded ldsc_out.\n")
} else if (length(loaded_ldsc) == 1) {
  ldsc_out <- get(loaded_ldsc[1])
  cat("Loaded LDSC object as ldsc_out.\n")
} else {
  stop("Could not find ldsc_out inside ldsc_out.RData.")
}

# =========================
# LOAD SNPs OBJECT
# =========================

if (!file.exists(snps_file)) {
  stop("Missing SNPs file: ", snps_file)
}

loaded_snps <- load(snps_file)

if ("SNPs" %in% loaded_snps) {
  cat("Loaded SNPs object.\n")
} else if (length(loaded_snps) == 1) {
  SNPs <- get(loaded_snps[1])
  cat("Loaded SNP object as SNPs.\n")
} else {
  stop("Could not find SNPs object inside SNPs_sumstats.RData.")
}

# =========================
# FINAL MODEL: M7 RENAL 6-FACTOR MODEL
# =========================

model_M7_gwas <- '
BP =~ DBP + HTN + SBP
CARDIO =~ AF + HF + STROKE + TROPO
ADIPOSITY =~ BMI + WC
GLYCEMIC =~ A1C + T2D
RENAL =~ CRF + eGFR
LIPID =~ HDL + TG

BP ~~ CARDIO + ADIPOSITY + GLYCEMIC + RENAL + LIPID
CARDIO ~~ ADIPOSITY + GLYCEMIC + RENAL + LIPID
ADIPOSITY ~~ GLYCEMIC + RENAL + LIPID
GLYCEMIC ~~ RENAL + LIPID
RENAL ~~ LIPID

HTN ~~ 0*HTN
T2D ~~ 0*T2D
WC ~~ 0*WC
CRF ~~ 0*CRF

BP ~ SNP
CARDIO ~ SNP
ADIPOSITY ~ SNP
GLYCEMIC ~ SNP
RENAL ~ SNP
LIPID ~ SNP
'

# =========================
# RUN userGWAS
# =========================

cat("Running userGWAS for M7 renal 6-factor model...\n")

gwas_M7 <- userGWAS(
  covstruc = ldsc_out,
  SNPs = SNPs,
  estimation = "DWLS",
  model = model_M7_gwas,
  sub = c(
    "BP~SNP",
    "CARDIO~SNP",
    "ADIPOSITY~SNP",
    "GLYCEMIC~SNP",
    "RENAL~SNP",
    "LIPID~SNP"
  ),
  printwarn = FALSE,
  cores = 8,
  toler = FALSE,
  SNPSE = FALSE,
  parallel = TRUE,
  GC = "standard",
  MPI = FALSE,
  smooth_check = TRUE,
  fix_measurement = TRUE,
  Q_SNP = TRUE
)

save(
  gwas_M7,
  file = file.path(out_dir, "M7_renal_6factor_userGWAS.RData")
)

# =========================
# SAVE RESULTS
# =========================

factor_names <- c(
  "BP",
  "CARDIO",
  "ADIPOSITY",
  "GLYCEMIC",
  "RENAL",
  "LIPID"
)

for (i in seq_along(gwas_M7)) {
  
  df <- as.data.table(gwas_M7[[i]])
  
  full_file <- file.path(out_dir, paste0("M7_", factor_names[i], "_GWAS.tsv"))
  sig_file  <- file.path(out_dir, paste0("M7_", factor_names[i], "_GWAS_significant.tsv"))
  qsnp_file <- file.path(out_dir, paste0("M7_", factor_names[i], "_Q_SNP_significant.tsv"))
  clean_file <- file.path(out_dir, paste0("M7_", factor_names[i], "_GWAS_QSNP_clean.tsv"))
  
  fwrite(df, full_file, sep = "\t")
  
  if ("Pval_Estimate" %in% names(df)) {
    fwrite(df[Pval_Estimate < 5e-8], sig_file, sep = "\t")
  }
  
  if ("Q_SNP_pval" %in% names(df)) {
    fwrite(df[Q_SNP_pval < 5e-8], qsnp_file, sep = "\t")
    fwrite(df[Q_SNP_pval >= 5e-8 | is.na(Q_SNP_pval)], clean_file, sep = "\t")
  }
}

# =========================
# SUMMARY TABLE
# =========================

summary_list <- list()

for (i in seq_along(gwas_M7)) {
  
  df <- as.data.table(gwas_M7[[i]])
  
  n_total <- nrow(df)
  
  n_sig <- if ("Pval_Estimate" %in% names(df)) {
    nrow(df[Pval_Estimate < 5e-8])
  } else {
    NA_integer_
  }
  
  n_qsnp <- if ("Q_SNP_pval" %in% names(df)) {
    nrow(df[Q_SNP_pval < 5e-8])
  } else {
    NA_integer_
  }
  
  summary_list[[i]] <- data.table(
    Factor = factor_names[i],
    Total_SNPs = n_total,
    Significant_SNPs_P_5e_8 = n_sig,
    Q_SNP_Significant = n_qsnp
  )
}

summary_dt <- rbindlist(summary_list)

fwrite(
  summary_dt,
  file.path(out_dir, "M7_userGWAS_summary.tsv"),
  sep = "\t"
)

cat("\nDONE: M7 renal 6-factor userGWAS completed.\n")
cat("Outputs saved in:\n")
cat(out_dir, "\n")
```



