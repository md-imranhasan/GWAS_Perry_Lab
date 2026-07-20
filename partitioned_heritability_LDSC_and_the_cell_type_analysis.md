## The Corrected S-LDSC SLURM Script

#### Create this file (submit_sldsc_cts.sh) in your /scratch/negishi/hasan128/data/magma/ folder and submit it using sbatch.

``` 
#!/bin/bash
#SBATCH -A pdrineas
#SBATCH -p cpu
#SBATCH -q normal
#SBATCH -t 12:00:00
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=32G
#SBATCH --job-name=sldsc_H_cts
#SBATCH --error=%x-%J-%u.err
#SBATCH --output=%x-%J-%u.out

# ==========================================
# Partitioned Heritability & Cell-Type Analysis
# ==========================================

# Define Paths
# IMPORTANT: Point this to where your ldsc.py script actually lives!
# If ldsc.py is not in /depot/ppaschou/data/ldsc/, you must update this path.
LDSC_EXEC_DIR="/depot/ppaschou/data/ldsc" 

WORK_DIR="/scratch/negishi/hasan128/data/magma"
cd $WORK_DIR

# Your munged H-Factor sumstats
INPUT_SUMSTATS="H_Factor_LDSC.sumstats.gz"

# S-LDSC Reference Files (Now pointing to your extracted scratch folders)
REF_DIR="/scratch/negishi/hasan128/data/ldsc_ref"
BASELINE_DIR="$REF_DIR/baselineLD_v2.2/baselineLD."
WEIGHTS_DIR="$REF_DIR/weights_hm3_no_hla/weights."
CTS_FILE="$REF_DIR/Multi_tissue_gene_expr/Multi_tissue_gene_expr.txt" # or .cts depending on the extraction

echo "Starting S-LDSC Cell-Type Specific Analysis on SLURM..."
date

# ==========================================
# RUN THE --h2-cts PIPELINE
# ==========================================
# Ensure you have your LDSC conda environment activated before submitting!
# e.g., source activate ldsc

python $LDSC_EXEC_DIR/ldsc.py \
    --h2 $INPUT_SUMSTATS \
    --ref-ld-chr $BASELINE_DIR \
    --out H_Factor_CellType_MultiTissue \
    --ref-ld-chr-cts $CTS_FILE \
    --w-ld-chr $WEIGHTS_DIR

echo "=========================================="
echo "S-LDSC analysis successfully completed!"
date
echo "=========================================="
```







######### ACutal code for S - LDSC######

```
###############################################################################
###############################################################################
# Final LDSC Cell-Type Enrichment Plot Script
# Input:
#   /scratch/negishi/hasan128/data/ldsc_ref/Publication_Ready_CellType_Results.tsv
#
# Outputs:
#   1. Corrected table with FDR and Bonferroni
#   2. All-cell-type barplot
#   3. Top-30 readable barplot
#
# Handles duplicated tissue/cell-type names.
# Increased font size for publication-style figures.
###############################################################################

suppressPackageStartupMessages({
  library(data.table)
  library(ggplot2)
  library(stringr)
})

###############################################################################
# 1. Input and output paths
###############################################################################

in_file <- "/scratch/negishi/hasan128/data/ldsc_ref/Publication_Ready_CellType_Results.tsv"

out_dir <- "/scratch/negishi/hasan128/data/ldsc_ref/final_celltype_plots"
dir.create(out_dir, recursive = TRUE, showWarnings = FALSE)

trait_name <- "H_Factor_CellType_MultiTissue"

###############################################################################
# 2. Read LDSC cell-type result
###############################################################################

dt <- fread(in_file)

cat("Detected columns:\n")
print(names(dt))

required_cols <- c(
  "Name",
  "Coefficient",
  "Coefficient_std_error",
  "Coefficient_P_value"
)

missing_cols <- setdiff(required_cols, names(dt))

if (length(missing_cols) > 0) {
  stop("Missing columns: ", paste(missing_cols, collapse = ", "))
}

###############################################################################
# 3. Format numeric columns
###############################################################################

dt[, P := as.numeric(Coefficient_P_value)]
dt[, Coefficient := as.numeric(Coefficient)]
dt[, Coefficient_std_error := as.numeric(Coefficient_std_error)]

dt <- dt[!is.na(P) & P > 0]

###############################################################################
# 4. Multiple-testing correction
###############################################################################

dt[, FDR := p.adjust(P, method = "BH")]
dt[, Bonferroni_P := pmin(P * .N, 1)]
dt[, Bonferroni_Significant := P < 0.05 / .N]
dt[, minus_log10_P := -log10(P)]

dt[, Significant := fifelse(
  FDR < 0.05,
  "FDR < 0.05",
  fifelse(P < 0.05, "Nominal P < 0.05", "Not significant")
)]

###############################################################################
# 5. Clean cell-type/tissue names
###############################################################################

dt[, Name_clean := Name]

# Remove ontology numeric prefix but keep biological name.
# Example:
# A08.186.211.464.710.225.Entorhinal.Cortex -> Entorhinal Cortex
dt[, Name_clean := gsub("^A[0-9]+(\\.[0-9]+)+\\.", "", Name_clean)]

# Clean punctuation
dt[, Name_clean := gsub("_", " ", Name_clean)]
dt[, Name_clean := gsub("\\.\\.", " ", Name_clean)]
dt[, Name_clean := gsub("\\.", " ", Name_clean)]
dt[, Name_clean := gsub("\\s+", " ", Name_clean)]
dt[, Name_clean := trimws(Name_clean)]

###############################################################################
# 6. Sort by P-value and save corrected table
###############################################################################

dt <- dt[order(P)]

corrected_file <- file.path(
  out_dir,
  paste0(trait_name, "_celltype_with_FDR_Bonferroni.tsv")
)

fwrite(dt, corrected_file, sep = "\t")

cat("\nSaved corrected table:\n")
cat(corrected_file, "\n")

###############################################################################
# 7. Print summary
###############################################################################

cat("\n============================================================\n")
cat("LDSC Cell-Type Enrichment Summary\n")
cat("============================================================\n")

cat("Number of tests:", nrow(dt), "\n")
cat("Bonferroni threshold:", 0.05 / nrow(dt), "\n")
cat("FDR significant:", nrow(dt[FDR < 0.05]), "\n")
cat("Bonferroni significant:", nrow(dt[Bonferroni_Significant == TRUE]), "\n")

cat("\nTop 20 cell-type LDSC results:\n")

print(
  dt[1:min(20, .N),
     .(
       Name,
       Name_clean,
       Coefficient,
       Coefficient_std_error,
       P,
       FDR,
       Bonferroni_P,
       Significant,
       Bonferroni_Significant
     )]
)

###############################################################################
# 8. Plot all 207 annotations
#    Red = FDR significant
#    Blue = nominally significant
#    Gray = not significant
###############################################################################

# Create unique plotting IDs to avoid duplicated factor-level error
dt[, Plot_ID := paste0(sprintf("%03d", .I), "_", Name_clean)]
dt[, Name_plot := factor(Plot_ID, levels = Plot_ID)]

axis_labels <- dt$Name_clean
names(axis_labels) <- dt$Plot_ID

bonf_y <- -log10(0.05 / nrow(dt))

p_all <- ggplot(dt, aes(x = Name_plot, y = minus_log10_P, fill = Significant)) +
  geom_col(width = 0.8) +
  geom_hline(
    yintercept = bonf_y,
    linetype = "dashed",
    linewidth = 0.7,
    color = "gray25"
  ) +
  scale_x_discrete(labels = axis_labels) +
  scale_fill_manual(
    values = c(
      "FDR < 0.05" = "red3",
      "Nominal P < 0.05" = "royalblue3",
      "Not significant" = "gray75"
    ),
    drop = FALSE
  ) +
  labs(
    title = paste0(trait_name, " LDSC Cell-Type Enrichment"),
    subtitle = paste0(
      "All ",
      nrow(dt),
      " annotations shown; dashed line = Bonferroni threshold, P < 0.05/",
      nrow(dt)
    ),
    x = NULL,
    y = expression(-log[10](P)),
    fill = NULL
  ) +
  theme_classic(base_size = 18) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5, size = 24),
    plot.subtitle = element_text(hjust = 0.5, size = 16),
    axis.text.x = element_text(
      angle = 90,
      hjust = 1,
      vjust = 0.5,
      size = 10,
      color = "black"
    ),
    axis.text.y = element_text(size = 16, color = "black"),
    axis.title.y = element_text(face = "bold", size = 20),
    legend.text = element_text(size = 16),
    legend.position = "top",
    axis.line = element_line(color = "black", linewidth = 0.6),
    axis.ticks = element_line(color = "black", linewidth = 0.5)
  )
p_all
ggsave(
  file.path(out_dir, paste0(trait_name, "_all_celltypes_barplot.pdf")),
  p_all,
  width = 30,
  height = 8,
  device = cairo_pdf
)

ggsave(
  file.path(out_dir, paste0(trait_name, "_all_celltypes_barplot.png")),
  p_all,
  width = 30,
  height = 8,
  dpi = 300
)

###############################################################################
# 9. Top-30 readable plot
###############################################################################

top_n <- 30
top_dt <- copy(dt[1:min(top_n, .N)])

top_dt[, Plot_ID := paste0(sprintf("%03d", .I), "_", Name_clean)]
top_dt[, Name_plot := factor(Plot_ID, levels = rev(Plot_ID))]

top_axis_labels <- top_dt$Name_clean
names(top_axis_labels) <- top_dt$Plot_ID

p_top <- ggplot(top_dt, aes(x = Name_plot, y = minus_log10_P, fill = Significant)) +
  geom_col(width = 0.75) +
  geom_hline(
    yintercept = bonf_y,
    linetype = "dashed",
    linewidth = 0.7,
    color = "gray25"
  ) +
  scale_x_discrete(labels = top_axis_labels) +
  scale_fill_manual(
    values = c(
      "FDR < 0.05" = "red3",
      "Nominal P < 0.05" = "royalblue3",
      "Not significant" = "gray75"
    ),
    drop = FALSE
  ) +
  coord_flip() +
  labs(
    title = paste0("Top ", nrow(top_dt), " LDSC Cell-Type Enrichment Results"),
    subtitle = paste0(
      trait_name,
      "; dashed line = Bonferroni threshold"
    ),
    x = NULL,
    y = expression(-log[10](P)),
    fill = NULL
  ) +
  theme_classic(base_size = 18) +
  theme(
    plot.title = element_text(face = "bold", hjust = 0.5, size = 24),
    plot.subtitle = element_text(hjust = 0.5, size = 16),
    axis.text.y = element_text(size = 16, color = "black"),
    axis.text.x = element_text(size = 16, color = "black"),
    axis.title.x = element_text(face = "bold", size = 20),
    legend.text = element_text(size = 16),
    legend.position = "top",
    axis.line = element_line(color = "black", linewidth = 0.6),
    axis.ticks = element_line(color = "black", linewidth = 0.5)
  )
p_top
ggsave(
  file.path(out_dir, paste0(trait_name, "_top30_celltypes_barplot.pdf")),
  p_top,
  width = 14,
  height = 11,
  device = cairo_pdf
)

ggsave(
  file.path(out_dir, paste0(trait_name, "_top30_celltypes_barplot.png")),
  p_top,
  width = 14,
  height = 11,
  dpi = 300
)

###############################################################################
# 10. Save significant-only tables
###############################################################################

fwrite(
  dt[FDR < 0.05],
  file.path(out_dir, paste0(trait_name, "_FDR_significant_only.tsv")),
  sep = "\t"
)

fwrite(
  dt[Bonferroni_Significant == TRUE],
  file.path(out_dir, paste0(trait_name, "_Bonferroni_significant_only.tsv")),
  sep = "\t"
)

###############################################################################
# 11. Final message
###############################################################################

cat("\n============================================================\n")
cat("DONE. Final files saved in:\n")
cat(out_dir, "\n")
cat("============================================================\n")

cat("\nMain interpretation:\n")
cat("FDR-significant annotations should be reported as the main cell-type enrichment result.\n")
cat("Bonferroni-significant annotations are the strictest results.\n")

```
