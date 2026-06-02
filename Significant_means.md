when we say significant, 99% of the times we mean after correction because we run so many tests.
We usually do fdr or bonferroni, but never show raw results, unless nothing is significant so we have to make it clear.


#FDR-Significant Genetic Correlations with Cardio-Renal-Metabolic (CRM) Trait
#multipule testing


``` r
#!/usr/bin/env Rscript
# ==============================================================================
# LDSC CRM vs ALL Multiple-Testing-Corrected Forest Plot
# Target: Cardio-Renal-Metabolic (CRM)
# ==============================================================================

library(ggplot2)
library(dplyr)
library(readr)
library(stringr)
library(tibble)
library(grid)

input_file <- "/scratch/negishi/hasan128/data/rg_crm_80/CRM_vs_ALL.log"

# -----------------------------
# 1. Read LDSC log summary table
# -----------------------------

lines <- readLines(input_file)

start_line <- grep("^Summary of Genetic Correlation Results", lines)

if (length(start_line) == 0) {
  stop("Could not find 'Summary of Genetic Correlation Results' in the LDSC log.")
}

summary_lines <- lines[(start_line + 1):length(lines)]

summary_lines <- summary_lines[
  grepl("^p1\\s+p2\\s+rg", summary_lines) |
    grepl("\\.sumstats\\.gz", summary_lines)
]

rg_data <- read_table(
  paste(summary_lines, collapse = "\n"),
  col_names = TRUE,
  show_col_types = FALSE
)

# -----------------------------
# 2. Clean trait names
# -----------------------------

rg_data <- rg_data %>%
  mutate(
    Trait1 = str_remove(p1, "\\.sumstats\\.gz$"),
    Trait2 = str_remove(p2, "\\.sumstats\\.gz$")
  )

# -----------------------------
# 3. Trait category mapping
# -----------------------------

trait_map <- tribble(
  ~Trait, ~Category, ~Trait_Label,
  "AAA_EUR_munge", "Gastrointestinal", "Abdominal Aortic Aneurysm",
  "Acne_Vulgaris_EUR_munge", "Immune", "Acne Vulgaris",
  "Acute_Appendicitis_EUR_munge", "Gastrointestinal", "Acute Appendicitis",
  "ADHD_EUR_Munge", "Psychiatric", "ADHD",
  "AFF_EUR_Munge", "Cardiometabolic", "Atrial Fibrillation and Flutter",
  "Age_First_Birth_EUR_Munge", "Reproductive/Endocrine", "Age at First Birth",
  "Alcohol_consumption_EUR_Munge", "Lifestyle", "Alcohol Consumption",
  "Alcohol_use_EUR", "Lifestyle", "Alcohol Use",
  "Anxiety_disorder_EUR", "Psychiatric", "Anxiety Disorder",
  "Asthama_EUR", "Immune", "Asthma",
  "Basophil_Percentage_EUR", "Laboratory & Physical Findings", "Basophil Percentage",
  "Bipolar_disorder_EUR", "Psychiatric", "Bipolar Disorder",
  "Blood_Clot_EUR", "Immune", "Blood Clot",
  "BMI_EUR", "Laboratory & Physical Findings", "BMI",
  "Body_fat_EUR", "Laboratory & Physical Findings", "Body Fat",
  "Breast_cancer_EUR", "Cancer", "Breast Cancer Female",
  "cannabis_use_order_EUR", "Psychiatric", "Cannabis Use Disorder",
  "celiac_EUR", "Immune", "Celiac Disease",
  "cigarettes_per_day_EUR", "Lifestyle", "Cigarettes Per Day",
  "Copd_EUR", "Cardiometabolic", "Chronic Obstructive Pulmonary Disease",
  "Covid_19_EUR", "Infectious", "COVID-19 Infection",
  "Creatinine_EUR", "Laboratory & Physical Findings", "Creatinine",
  "Crohns_Disease_EUR", "Immune", "Crohn's Disease",
  "Daytime_EUR", "Lifestyle", "Daytime Napping",
  "DBP_EUR", "Laboratory & Physical Findings", "DBP",
  "Depression_EUR", "Psychiatric", "Depression",
  "Diverticulosis_EUR", "Gastrointestinal", "Diverticulosis and Diverticulitis",
  "Education_year_EUR", "Miscellaneous", "Education Years",
  "Eosinophil_EUR", "Laboratory & Physical Findings", "Eosinophil Percentage",
  "Epilepsy_EUR", "Psychiatric", "Genetic Generalized Epilepsy",
  "Esophageal_Cancer_EUR", "Cancer", "Esophageal Cancer",
  "Ever_Smoked_EUR", "Lifestyle", "Ever Smoked",
  "Fetal_Birth_EUR", "Reproductive/Endocrine", "Fetal Birth Weight",
  "Gout_EUR", "Gastrointestinal", "Gout",
  "HDL_EUR", "Laboratory & Physical Findings", "HDL",
  "Heart_failure_EUR", "Cardiometabolic", "Heart Failure",
  "Hernia_EUR", "Gastrointestinal", "Inguinal Hernia",
  "Hypercholesterolemia_EUR", "Laboratory & Physical Findings", "Hypercholesterolemia",
  "Hypothyroidism_EUR", "Cardiometabolic", "Hypothyroidism",
  "IBD_EUR", "Immune", "Inflammatory Bowel Disease",
  "Insomnia_EUR", "Lifestyle", "Insomnia",
  "Insulin_Growth_Factor_1_EUR", "Laboratory & Physical Findings", "Insulin-like Growth Factor 1",
  "IS_EUR", "Cardiometabolic", "Ischemic Stroke",
  "LDL_EUR", "Laboratory & Physical Findings", "LDL",
  "Lung_cancer_EUR", "Cancer", "Lung Cancer",
  "Medication_use_EUR", "Miscellaneous", "Medication Use",
  "Menarche_Age_EUR", "Reproductive/Endocrine", "Age at Menarche",
  "Migrain_EUR", "Neurological", "Migraine",
  "Multiple_sclerosis_EUR", "Immune", "Multiple Sclerosis",
  "Number_of_children_ever_born_EUR", "Reproductive/Endocrine", "Number of Children Ever Born",
  "parkinson_EUR", "Neurodegenerative", "Parkinson's Disease",
  "Physical_Activity_EUR", "Lifestyle", "Physical Activity",
  "Polycystic_ovaries_EUR", "Reproductive/Endocrine", "Polycystic Ovary Syndrome",
  "Prostate_Cancer_EUR", "Cancer", "Prostate Cancer",
  "Psoriasis_EUR", "Immune", "Psoriasis",
  "RA_EUR", "Immune", "Rheumatoid Arthritis",
  "RBC_EUR", "Laboratory & Physical Findings", "RBC",
  "Reticulocyte_EUR", "Laboratory & Physical Findings", "Reticulocyte Count",
  "Risk_tolerance_EUR", "Lifestyle", "General Risk Tolerance",
  "Schizophrenia_EUR", "Psychiatric", "Schizophrenia",
  "Sleep_duration_EUR", "Lifestyle", "Sleep Duration",
  "systemic_lupus_erythematosus_EUR", "Immune", "Lupus",
  "T1D_EUR", "Immune", "Type 1 Diabetes",
  "T2D_EUR", "Cardiometabolic", "Type 2 Diabetes",
  "Testosterone_Male_EUR", "Reproductive/Endocrine", "Testosterone Male",
  "Total_Bilirubin_EUR", "Laboratory & Physical Findings", "Total Bilirubin",
  "Total_Protein_EUR", "Laboratory & Physical Findings", "Total Protein",
  "Tourette_Syndrome_EUR", "Psychiatric", "Tourette Syndrome",
  "Venous_EUR", "Cardiometabolic", "Venous Thromboembolism",
  "Vit_D_EUR", "Laboratory & Physical Findings", "Vitamin D"
)

# -----------------------------
# 4. Prepare data with correction
# -----------------------------

plot_data <- rg_data %>%
  mutate(
    rg = suppressWarnings(as.numeric(rg)),
    se = suppressWarnings(as.numeric(se)),
    z = suppressWarnings(as.numeric(z)),
    p = suppressWarnings(as.numeric(p))
  ) %>%
  filter(!is.na(rg), !is.na(se), !is.na(p)) %>%
  mutate(
    ci_low = rg - 1.96 * se,
    ci_high = rg + 1.96 * se
  ) %>%
  left_join(trait_map, by = c("Trait2" = "Trait")) %>%
  mutate(
    Category = ifelse(is.na(Category), "Uncategorized", Category),
    Trait_Label = ifelse(is.na(Trait_Label), Trait2, Trait_Label)
  )

n_tests <- nrow(plot_data)
bonf_threshold <- 0.05 / n_tests

plot_data <- plot_data %>%
  mutate(
    p_fdr = p.adjust(p, method = "BH"),
    p_bonf = p.adjust(p, method = "bonferroni"),
    FDR_significant = p_fdr < 0.05,
    Bonferroni_significant = p_bonf < 0.05,
    Direction_Check = case_when(
      Trait2 == "HDL_EUR" & rg < 0 ~ "HDL expected negative",
      Trait2 == "HDL_EUR" & rg > 0 ~ "Check HDL direction",
      Trait2 == "LDL_EUR" & rg > 0 ~ "LDL expected positive",
      Trait2 == "LDL_EUR" & rg < 0 ~ "Check LDL direction",
      TRUE ~ "Not checked"
    )
  )

# -----------------------------
# 5. Save tables
# -----------------------------

write_tsv(plot_data, "CRM_vs_ALL_rg_all_results_with_FDR_Bonferroni.tsv")

fdr_data <- plot_data %>%
  filter(FDR_significant) %>%
  arrange(rg)

bonf_data <- plot_data %>%
  filter(Bonferroni_significant) %>%
  arrange(rg)

write_tsv(fdr_data, "CRM_vs_ALL_FDR_significant_only.tsv")
write_tsv(bonf_data, "CRM_vs_ALL_Bonferroni_significant_only.tsv")

write_tsv(
  plot_data %>% filter(Direction_Check != "Not checked"),
  "CRM_vs_ALL_HDL_LDL_direction_check.tsv"
)

uncategorized <- plot_data %>%
  filter(Category == "Uncategorized") %>%
  select(Trait2)

write_tsv(uncategorized, "CRM_vs_ALL_uncategorized_traits.tsv")

if (nrow(uncategorized) > 0) {
  print("WARNING: Uncategorized traits found:")
  print(uncategorized)
} else {
  print("All traits are categorized.")
}

cat("Number of tested correlations:", n_tests, "\n")
cat("Bonferroni threshold:", bonf_threshold, "\n")
cat("FDR-significant traits:", nrow(fdr_data), "\n")
cat("Bonferroni-significant traits:", nrow(bonf_data), "\n")

# -----------------------------
# 6. Main plot: FDR-significant only
# -----------------------------

plot_fdr <- fdr_data %>%
  arrange(rg) %>%
  mutate(Trait_Label = factor(Trait_Label, levels = unique(Trait_Label)))

p_fdr <- ggplot(plot_fdr, aes(x = rg, y = Trait_Label, color = Category)) +
  geom_errorbarh(
    aes(xmin = ci_low, xmax = ci_high),
    height = 0.25,
    linewidth = 1.0,
    alpha = 1
  ) +
  geom_point(
    shape = 21,
    fill = "white",
    size = 3.5,
    stroke = 1.2
  ) +
  geom_vline(
    xintercept = 0,
    linetype = "dashed",
    color = "gray40",
    linewidth = 0.6
  ) +
  theme_classic() +
  labs(
    title = "FDR-Significant Genetic Correlations with Cardio-Renal-Metabolic (CRM) Trait",
    subtitle = "Multiple-testing corrected significance: FDR < 0.05",
    x = expression("Genetic correlation (" * r[g] * ") with 95% CI"),
    y = NULL,
    color = "Category"
  ) +
  theme(
    panel.border = element_rect(color = "black", fill = NA, linewidth = 0.5),
    axis.text.y = element_text(size = 8.5, color = "black"),
    axis.text.x = element_text(size = 10, color = "black"),
    axis.title.x = element_text(size = 12, margin = margin(t = 10)),
    plot.title = element_text(size = 14, face = "bold"),
    plot.subtitle = element_text(size = 11),
    legend.position = "right",
    legend.key.size = unit(1.1, "lines"),
    axis.ticks.y = element_blank()
  ) +
  coord_cartesian(xlim = c(-1, 1))
p_fdr
plot_height_fdr <- max(8, nrow(plot_fdr) * 0.26)

ggsave(
  "CRM_vs_ALL_rg_forest_plot_FDR_significant.pdf",
  p_fdr,
  width = 12,
  height = plot_height_fdr,
  dpi = 300
)

ggsave(
  "CRM_vs_ALL_rg_forest_plot_FDR_significant.png",
  p_fdr,
  width = 12,
  height = plot_height_fdr,
  dpi = 300
)

# -----------------------------
# 7. Secondary plot: Bonferroni-significant only
# -----------------------------

plot_bonf <- bonf_data %>%
  arrange(rg) %>%
  mutate(Trait_Label = factor(Trait_Label, levels = unique(Trait_Label)))

p_bonf <- ggplot(plot_bonf, aes(x = rg, y = Trait_Label, color = Category)) +
  geom_errorbarh(
    aes(xmin = ci_low, xmax = ci_high),
    height = 0.25,
    linewidth = 1.0,
    alpha = 1
  ) +
  geom_point(
    shape = 21,
    fill = "white",
    size = 3.5,
    stroke = 1.2
  ) +
  geom_vline(
    xintercept = 0,
    linetype = "dashed",
    color = "gray40",
    linewidth = 0.6
  ) +
  theme_classic() +
  labs(
    title = "Bonferroni-Significant Genetic Correlations with Cardio-Renal-Metabolic (CRM) Trait",
    subtitle = paste0(
      "Multiple-testing corrected significance: P < ",
      signif(bonf_threshold, 3)
    ),
    x = expression("Genetic correlation (" * r[g] * ") with 95% CI"),
    y = NULL,
    color = "Category"
  ) +
  theme(
    panel.border = element_rect(color = "black", fill = NA, linewidth = 0.5),
    axis.text.y = element_text(size = 8.5, color = "black"),
    axis.text.x = element_text(size = 10, color = "black"),
    axis.title.x = element_text(size = 12, margin = margin(t = 10)),
    plot.title = element_text(size = 14, face = "bold"),
    plot.subtitle = element_text(size = 11),
    legend.position = "right",
    legend.key.size = unit(1.1, "lines"),
    axis.ticks.y = element_blank()
  ) +
  coord_cartesian(xlim = c(-1, 1))

plot_height_bonf <- max(8, nrow(plot_bonf) * 0.26)

ggsave(
  "CRM_vs_ALL_rg_forest_plot_Bonferroni_significant.pdf",
  p_bonf,
  width = 12,
  height = plot_height_bonf,
  dpi = 300
)

ggsave(
  "CRM_vs_ALL_rg_forest_plot_Bonferroni_significant.png",
  p_bonf,
  width = 12,
  height = plot_height_bonf,
  dpi = 300
)

print("Done: FDR and Bonferroni corrected tables and plots created.")

```


