
# Comparative Evaluation of Relative Finding Methods: PLINK vs. GERMLINE

## Project Overview

This project analyzes genetic relatedness using data from the **1000 Genomes Project (Phase 3)**.  
The goal is to compare two commonly used tools for detecting identity-by-descent (IBD):

- **PLINK (v1.9)**
- **GERMLINE (v1.5)**

PLINK estimates relatedness using probabilistic IBD statistics (Z0, Z1, Z2, PI_HAT), while GERMLINE directly detects shared haplotype segments between individuals. The project compares their outputs and examines how well they identify different degrees of relatedness.

---

## Dataset

We use genotype data from the **1000 Genomes Project Phase 3 release** (2504 samples):

https://ftp.1000genomes.ebi.ac.uk/vol1/ftp/release/20130502/

Because the full dataset is large, we extracted a **small subset of samples with known relatedness** to make the analysis computationally feasible.

The data are phased, which allows GERMLINE to detect IBD segments.

---

## Project Structure
Example structure of the repository:

```
# TODO
project/
│
├── data/                # processed VCF files or subset data
├── scripts/             # analysis scripts
├── results/             # outputs from PLINK and GERMLINE
├── main.ipynb           # main analysis notebook
└── README.md
```

---

## Analysis Scripts

The main analysis is performed in:

```
main.ipynb
```

The notebook performs:

1. preprocessing of VCF data
2. running PLINK relatedness estimation
3. running GERMLINE segment detection
4. comparing outputs from both tools
5. computing summary metrics such as shared IBD

PLINK is used to compute relatedness statistics (Z0, Z1, Z2, PI_HAT).

GERMLINE is used to detect IBD segments between phased haplotypes.

---

## Running the Analysis

### Requirements

Install the following tools:

```
PLINK v1.9
GERMLINE v1.5
Python 3.x
```

### Running the notebook

Open the notebook:

```
jupyter notebook main.ipynb
```


Run all cells to reproduce the analysis.

Note that the notebook calls several external tools using shell commands (prefixed with `!`).  
These commands require the following software to be installed and accessible from the command line:

- PLINK v1.9
- GERMLINE v1.5

The notebook uses commands such as:
```
!plink --vcf input.vcf --genome --out plink_output
!germline -input input.hap -output germline_output
```

These commands are executed directly inside the notebook to perform relatedness estimation and IBD segment detection.

---
```
# TODO
```
## Results So Far

So far we have:

- successfully extracted a subset of samples from the 1000 Genomes dataset
- computed relatedness statistics using **PLINK**
- detected shared IBD segments using **GERMLINE**

Preliminary observations:

- PLINK quickly identifies related individuals using **PI_HAT values**
- GERMLINE provides **segment-level IBD information**, which can be used to compute total shared genomic length
- parent–offspring and full-sibling relationships have similar PI_HAT values but can be distinguished using **Z1/Z2 statistics or segment patterns**

Future Work

Further work will compare the performance of PLINK and GERMLINE in terms of:
- relationship classification accuracy
- computational cost
- differences in detected IBD sharing
