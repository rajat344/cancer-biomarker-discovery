# Dataset Documentation

## Project

**Cross-Cohort Transcriptomic Biomarker Discovery in Lung Adenocarcinoma**

---

## Disease Scope

This project focuses specifically on **lung adenocarcinoma (LUAD)**, a major histological subtype of non-small cell lung cancer (NSCLC).

The findings should therefore be interpreted as LUAD-specific rather than representative of every NSCLC subtype.

---

# 1. Discovery Dataset

## GEO Accession

**GSE75037**

## Dataset Description

Expression profiling of **83 matched pairs of lung adenocarcinomas and non-malignant adjacent tissue**.

## Cohort

| Category | Samples |
|---|---:|
| Tumor | 83 |
| Non-malignant | 83 |
| Total | 166 |
| Matched pairs | 83 |

Every patient pair contains one tumor sample and one matched non-malignant adjacent tissue sample.

## Platform

**Illumina HumanWG-6 v3.0 expression beadchip**

Platform accession: **GPL6884**

## Expression Data

The GEO processed expression matrix contains normalized, log2-transformed expression values.

The dataset contains **48,803 probe-level measurements across 166 samples** before probe-to-gene processing.

## Role in Project

GSE75037 was used as the **discovery cohort** for:

- Quality control
- Exploratory data analysis
- Principal Component Analysis (PCA)
- Probe-to-gene mapping
- Paired differential expression analysis
- Multiple-testing correction
- Candidate biomarker selection
- Pathway enrichment
- Machine-learning evaluation

---

# 2. External Validation Dataset

## GEO Accession

**GSE43458**

## Dataset Description

Gene expression profiling of **lung adenocarcinomas and normal lung tissue**.

## Cohort

| Category | Samples |
|---|---:|
| Tumor | 80 |
| Normal | 30 |
| Total | 110 |

The tumor cohort contains:

- 40 never-smoker tumors
- 40 smoker tumors

The 30 normal lung samples are associated with never-smoker cases.

## Platform

**Affymetrix Human Gene 1.0 ST Array**

Platform accession: **GPL6244**

## Expression Data

The GEO processed expression matrix contains RMA-normalized, log2-transformed expression measurements.

## Role in Project

GSE43458 was used strictly as the **external validation cohort**.

Candidate biomarkers were selected using the discovery cohort before being evaluated in GSE43458.

The validation dataset was therefore not used to discover or select the final biomarkers.

---

# 3. Why Two Cohorts?

The purpose of using two datasets is to separate **biomarker discovery** from **biomarker validation**.

### Discovery

Potentially informative genes were identified using GSE75037.

### Validation

The pre-selected candidate biomarkers were then tested in GSE43458.

This provides a stronger evaluation than measuring biomarker performance only within the dataset used for discovery.

---

# 4. Cross-Platform Validation

The two datasets were generated using different microarray platforms:

| Dataset | Platform |
|---|---|
| GSE75037 | Illumina HumanWG-6 v3.0 |
| GSE43458 | Affymetrix Human Gene 1.0 ST |

Because the platforms use different probe designs, candidate genes were mapped to gene symbols before being evaluated across cohorts.

This creates a **cross-platform external validation** setting rather than simply testing the same probes on the same platform.

---

# 5. Dataset Selection

Several GEO datasets were considered during project design.

## GSE32863

GSE32863 was investigated as a potential validation dataset because it also contains lung adenocarcinoma and adjacent non-tumor tissue and uses the Illumina GPL6884 platform.

However, sample-pair identifiers showed substantial overlap with GSE75037.

Therefore, GSE32863 was **not treated as an independent validation cohort**.

This decision avoids using overlapping patients to claim external validation.

## GSE43458

GSE43458 was selected as the primary external validation cohort because it provides LUAD tumor and normal lung samples from a separate study and uses a different microarray platform.

---

# 6. Analysis Strategy

The complete analysis follows:

```text
GSE75037 Discovery Cohort
            │
            ├── Metadata validation
            │
            ├── Quality control
            │
            ├── Exploratory analysis
            │
            ├── PCA
            │
            ├── Probe → Gene mapping
            │
            ├── Paired differential expression
            │
            ├── Multiple-testing correction
            │
            ├── Candidate biomarker selection
            │
            └── Pathway enrichment
                       │
                       ▼
              Locked candidate genes
                       │
                       ▼
             GSE43458 Validation Cohort
                       │
                       ├── Cross-platform gene mapping
                       │
                       ├── Individual ROC/AUC
                       │
                       └── Multi-gene panel validation
7. Statistical Considerations

The discovery dataset contains paired tumor–normal samples.

Therefore, the primary differential-expression analysis uses the matched-pair structure rather than treating all tumor and normal samples as independent observations.

Expression values are already log2-transformed microarray measurements.

Consequently, RNA-seq-specific count-based methods such as DESeq2 are not directly appropriate for this processed expression matrix.

Multiple hypothesis testing is controlled using the Benjamini–Hochberg false discovery rate (FDR) procedure.

Genes were considered significantly differentially expressed using:

FDR < 0.05
|log2FC| ≥ 1

Candidate biomarker selection additionally considered consistency across matched patient pairs.

8. Biomarker Validation

Candidate biomarkers were selected from the discovery cohort and then evaluated in the external validation cohort.

The validation analysis included:

Individual candidate ROC curves
ROC-AUC calculation
Direction-aware expression scoring
Multi-gene panel evaluation

The strongest individual candidate identified in the validation analysis was ITLN2.

The final multi-gene panel was also evaluated for its ability to distinguish LUAD tumor samples from normal lung samples.

9. Machine-Learning Analysis

Machine-learning classification was performed using logistic regression with 5-fold GroupKFold cross-validation.

Patient-pair identifiers were used as grouping variables so that matched tumor and normal samples from the same patient remained within the same fold.

This reduces the possibility of patient-level information leakage between training and test folds.

The machine-learning analysis reports:

ROC-AUC
Accuracy
Sensitivity
Specificity
Confusion matrix
10. Data Availability

The datasets used in this project are publicly available through the NCBI Gene Expression Omnibus (GEO).

Discovery

GSE75037

External Validation

GSE43458

Raw and processed dataset files are intentionally excluded from Git version control because of their size.

The repository documents the dataset accession numbers, analysis workflow, computational environment, and generated results.

11. Important Limitations

This project represents computational biomarker discovery and external validation rather than clinical diagnostic validation.

Important limitations include:

The study focuses on LUAD rather than all NSCLC subtypes.
The datasets are microarray-based rather than RNA-seq.
Discovery and validation cohorts were generated using different microarray platforms.
The external validation cohort has differences in smoking-status composition.
The validation cohort contains 80 tumors and 30 normal samples.
Cross-platform gene mapping may introduce technical differences.
Machine-learning evaluation uses a pre-specified biomarker panel selected from the discovery analysis rather than performing feature selection independently inside every cross-validation fold.
Experimental validation and larger clinical cohorts would be required before clinical translation.

Therefore, ROC-AUC values should be interpreted as computational cohort-level discrimination, not as proof of clinical diagnostic utility.

12. Reproducibility

The computational environment is documented in:

environment.yml

Main tools used include:

Python
pandas
NumPy
SciPy
Matplotlib
scikit-learn
GSEApy
Jupyter

The analysis notebooks and generated result tables/figures are included in the repository.

13. Summary

The dataset design enables a discovery-and-validation workflow in which:

GSE75037 → biomarker discovery

and

GSE43458 → external validation

This separation is central to evaluating whether transcriptomic biomarkers identified in one LUAD cohort retain discriminatory performance in an independent cohort generated using a different microarray platform.