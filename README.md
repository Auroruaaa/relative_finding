
# Comparative Evaluation of Relative Finding Methods: PLINK vs. GERMLINE

## Project Overview

This project analyzes genetic relatedness using data from the **1000 Genomes Project (Phase 3)**.  
The goal is to compare two commonly used tools for detecting identity-by-descent (IBD):

- **PLINK (v1.9)**
- **GERMLINE (v1.5)**

PLINK estimates relatedness using probabilistic IBD statistics (Z0, Z1, Z2, PI_HAT), while GERMLINE directly detects shared haplotype segments between individuals. The project compares their outputs and examines how well they identify different degrees of relatedness.

---

## Installation 

To run this project, several external tools are required.  
We recommend installing them using **conda**.

Create and activate the environment:

```
conda create -n bio_bench python=3.9 -y
conda activate bio_bench
```


Install the required tools:
```
conda install -c bioconda plink -y
conda install -c bioconda bcftools -y
conda install -c bioconda vcftools -y
conda install -c conda-forge pandas matplotlib seaborn -y
```


These packages are used for processing genotype data and generating the analysis plots.

---

Download PLINK using:
https://www.cog-genomics.org/plink/

---

## Installing GERMLINE

We use the **GERMLINE implementation from the Gusev Lab GitHub repository**:

https://github.com/gusevlab/germline

Clone the repository:
```
git clone https://github.com/gusevlab/germline.git

cd germline
```

Then compile the program:
```
make
```


After running `make`, the GERMLINE executable will be created and can be used in the analysis pipeline.

---

## Basic Usage

### PLINK

We use PLINK to estimate pairwise relatedness using IBD statistics.

```
plink --bfile ./data/toy_plink --genome --out plink_ibd
```


This command generates a `.genome` file containing statistics such as **Z0, Z1, Z2, and PI_HAT**.

---

### GERMLINE

GERMLINE is used to detect shared IBD segments between individuals.

Before running GERMLINE, we convert the dataset into the required format.

```
plink --bfile ./data/ps2_ibd.lwk --recode vcf --out ps2_ibd.lwk
```


This produces a VCF file that can be used for further processing.

Run GERMLINE with:
```
./tools/germline/germline
-input ./data/full_samples/full_germline_pruned.ped
./data/full_samples/full_germline_pruned.map
-output germline_full_out
-min_m 3
-bits 16
```

The output file contains the detected IBD segments between pairs of individuals.


## Dataset

We use genotype data from the **1000 Genomes Project Phase 3 release** (2504 samples):

https://ftp.1000genomes.ebi.ac.uk/vol1/ftp/release/20130502/

Because the full dataset is large, we extracted a **small subset of samples with known relatedness** to make the analysis computationally feasible.

The data are phased, which allows GERMLINE to detect IBD segments.

---

## Project Structure
Example structure of the repository:

```
project/
│
├── data/                
├── output/             
├── src/ 
├──command.ipynb          
├── main.ipynb        
└── README.md
└── plot_5.ipynb
└── plot_chr22.ipynb
```

---

## Analysis Scripts

The main analysis is performed in:

main.ipynb

The notebook performs the following steps:

1. **Preprocessing**
   - extract a subset of samples from the 1000 Genomes VCF dataset
   - prepare genotype data for downstream analysis

2. **Quality Control (QC)**
   - apply standard QC filters to remove low-quality variants and samples
   - ensure reliable genotype data before relatedness analysis

3. **LD Pruning**
   - prune correlated SNPs to reduce linkage disequilibrium
   - retain approximately independent variants for relatedness estimation

4. **PLINK relatedness estimation**

PLINK is used to compute relatedness statistics such as **Z0, Z1, Z2, and PI_HAT**.

5. **GERMLINE segment detection**

GERMLINE detects **IBD segments between phased haplotypes**.

6. **Comparison of outputs**

The outputs from both tools are compared using summary metrics such as shared IBD and inferred relatedness.

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

## Results So Far

So far we have:

- successfully extracted a subset of samples from the 1000 Genomes dataset
- computed relatedness statistics using **PLINK**
- detected shared IBD segments using **GERMLINE**

Preliminary observations:

- PLINK quickly identifies related individuals using **PI_HAT values**
- GERMLINE provides **segment-level IBD information**, which can be used to compute total shared genomic length
- parent–child and sibling relationships have similar PI_HAT values and are classified as first-order relationship; and it is distinguished with second-order relationship using **Z1/Z2 statistics or segment patterns**

## Visualization of Results

Several notebooks are used to visualize and analyze the results:

- `plot_5.ipynb` – visualization of results for **chromosomes 1–5**
- `plot_chr22.ipynb` – visualization of results for **chromosome 22**

These notebooks generate plots including:

- **Z0–Z1 scatter plots** from PLINK outputs to visualize relatedness patterns
- **PI_HAT distribution plots** showing the distribution of estimated relatedness
- **IBD segment length distributions** from GERMLINE outputs
- **colored relationship plots** using known relatedness labels
- **time and space efficiency comparisons** between PLINK and GERMLINE

These visualizations help compare how the two methods detect related individuals and characterize shared genomic segments.

## Future Work

Further improvements and experiments may include:
- **GERMLINE parameter tuning** Investigate the effect of GERMLINE parameters such as the minimum match length (`-min_m`) on IBD segment detection.
- **Segment length metrics** Currently GERMLINE outputs segment length primarily in **Mb**. Future work may explore preserving or converting segment lengths into **centimorgans (cM)** for better biological interpretation.
- **Scaling to additional chromosomes** The current experiments focus on **chromosomes 1–5**. Future analysis could include more chromosomes to evaluate scalability and robustness.
- **Relationship classification task** Use PLINK and GERMLINE outputs (e.g., PI_HAT, Z1+Z2, total IBD length) to build a simple classifier that predicts the degree of relatedness between individuals, and compare the classification performance of the two methods.
- **Population-level analysis** Extend the analysis to different population groups in the 1000 Genomes dataset and evaluate whether classification accuracy varies across populations.
- **Classification Task** Use relatedness statistics from PLINK and GERMLINE as features to classify the degree of relatedness between individuals.
