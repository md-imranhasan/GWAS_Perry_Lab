# GWAS_Perry_Lab

Here's a **summary** of your entire **GWAS analysis workflow**, formatted for easy uploading to **GitHub**:

---

# **GWAS Analysis Workflow**

This repository contains the steps and code used for performing **Genome-Wide Association Study (GWAS)** analysis, from data cleaning and quality control (QC) to running the analysis with **PLINK** and generating a **Manhattan plot** for visualizing SNP associations.

## **Steps in the Workflow:**

### **1. Data Preparation:**

#### **1.1 Load PCA Results**

The first step is to read and prepare the PCA results:

```r
pca <- read.table("D:/data/plink2_out/personal_dataset_clean_V6.evec", header = FALSE, stringsAsFactors = FALSE)
colnames(pca) <- c("SampleID", paste0("PC", 1:10), "Pop")
```

#### **1.2 Visualize PCA Results**

You can visualize the first two principal components (PC1 vs. PC2) and other pairwise combinations (e.g., PC3 vs. PC4):

```r
ggplot(pca, aes(x=PC1, y=PC2, color=Pop)) +
  geom_point() +
  theme_minimal() +
  labs(title="PCA: PC1 vs PC2")
```

---

### **2. Data Filtering and Handling Missing Values:**

#### **2.1 Handle Missing Values in GWAS Results**

After performing the **GWAS**, check for any missing values in the p-value column:

```r
sum(!is.finite(gwas_results$P)) # Count non-finite values in the p-value column
```

#### **2.2 Remove Non-Finite Rows**

Remove rows with non-finite values (`NA`, `NaN`, or `Inf`) in critical columns like **OR**, **SE**, and **P**:

```r
gwas_results_clean <- gwas_results[is.finite(gwas_results$P) & is.finite(gwas_results$OR) & 
                                    is.finite(gwas_results$SE) & is.finite(gwas_results$L95) & 
                                    is.finite(gwas_results$U95) & is.finite(gwas_results$STAT), ]
```

---

### **3. Create Covariate File for GWAS:**

#### **3.1 Prepare PCA Covariates**

The next step is to prepare the **covariate file** for GWAS by including **PC1 to PC10** and **FID/IID**:

```r
covar <- pca[, c("SampleID", "Pop", paste0("PC", 1:10))]
write.table(covar, file = "D:/data/covar_mds.txt", quote = FALSE, row.names = FALSE, col.names = TRUE, sep = "\t")
```

This creates a tab-delimited file (`covar_mds.txt`) with **FID**, **IID**, and **PC1 to PC10** for the GWAS analysis.

---

### **4. Run GWAS with PLINK:**

#### **4.1 Logistic Regression with Covariates**

Run the **GWAS** using **PLINK** with **logistic regression** for binary traits, adjusting for population structure using the **PCA covariates**:

```bash
plink \
  --bfile eur_personal_dataset_ld_pruned \
  --pheno phenotype.txt \
  --covar D:/data/covar_mds.txt \
  --logistic \
  --out D:/data/gwas_results_clean
```

This will generate the association results in the `gwas_results_clean.assoc.logistic` file.

---

### **5. Visualize Results with Manhattan Plot:**

#### **5.1 Generate the Manhattan Plot**

Using the cleaned GWAS results, generate a **Manhattan plot** to visualize the SNP associations:

```r
library(qqman)

manhattan(gwas_results_clean, chr="CHR", bp="BP", snp="SNP", p="P")
```

This will display a **Manhattan plot** showing SNP associations across the genome.

---

### **6. Optional: Outlier Detection**

#### **6.1 Multivariate and Univariate Outlier Detection**

You can detect **outliers** based on **PCA**:

```r
# Multivariate outliers (Mahalanobis distance)
mu4 <- colMeans(eur_ref)
S4  <- stats::cov(eur_ref)
D2 <- mahalanobis(pca_data[, pcs4, drop = FALSE], center = mu4, cov = S4)
cut_mv <- qchisq(0.99, df = length(pcs4))  # 99th percentile (alpha=0.01)

# Univariate outliers (±k·SD)
alpha_family <- 0.01
alpha_pc <- alpha_family / length(pcs4)
k <- qnorm(1 - alpha_pc/2)   # two-sided
```

#### **6.2 Generate Outlier List**

Create a list of personal outliers for removal in **PLINK**:

```r
personal_outliers <- subset(pca_data, is_personal & (outlier_mv | outlier_uni), select = c(SampleID))
outliers_df <- data.frame(FID = personal_outliers$SampleID, IID = personal_outliers$SampleID)
write.table(outliers_df, file = "D:/data/personal_outliers.txt", quote = FALSE, row.names = FALSE, col.names = FALSE)
```

---

### **7. Summary and Results:**

* **Data Preparation**: Process and QC both personal and 1000 Genomes data.
* **GWAS Execution**: Perform **GWAS** with **PLINK**, adjusting for PCA covariates.
* **Visualization**: Generate a **Manhattan plot** to visualize SNP associations.
* **Optional Outlier Removal**: Detect and remove outliers from the PCA space before running the analysis.

---

### **Files Generated:**

1. **`covar_mds.txt`**: Covariate file for GWAS (includes FID, IID, and PC1–PC10).
2. **`personal_outliers.txt`**: List of outlier samples based on PCA thresholds.
3. **`gwas_results_clean.assoc.logistic`**: GWAS results from logistic regression.
4. **Manhattan plot** generated from `gwas_results_clean`.

---

### **Next Steps:**

After completing the GWAS, you can analyze the **results** and perform additional steps like:

* **Post-GWAS analysis** (e.g., fine mapping, functional annotation).
* **Replication studies** using external datasets.

---

