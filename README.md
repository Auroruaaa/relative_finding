# Comparing PLINK and GERMLINE for Relative Detection

## Project Overview

In this project, we compare two commonly used tools for detecting related individuals in genetic data:

- **PLINK** (using `--genome`)
- **GERMLINE** (haplotype-based IBD segment detection)

The goal is to see how well each method can recover known relationships from the 1000 Genomes Project, using only chromosome 22 data.

The ground truth relationships are provided in:

`20140625_related_individuals.txt`

These include relationships such as:

- Parent  
- Sibling  
- Trio
- Second Order  

We extract those individuals from the chr22 VCF files and compare predicted relationships from PLINK and GERMLINE against the ground truth.

---

## What We Have Done So Far

### 1. Data Processing

- Parsed the ground truth file and extracted all related individuals.
- Located those individuals in the chr22 VCF files.
- Created a filtered VCF containing only those samples.
- Converted the data into PLINK format (.bed/.bim/.fam).
- Performed QC filtering and LD pruning (for PLINK analysis).

---

### 2. PLINK Analysis

Using:

```bash
plink --genome
