# RNAseq Snakemake Workflow

**Version: 202509**

![workflow diagram](images/RNAseq_workflow-diagram.png)

This workflow provides an end-to-end RNAseq analysis pipeline for **PDX**, **PDO**, and **Patient Tissue** samples, customized for the **HPC4Health Slurm cluster**.  
It integrates widely used bioinformatics tools with cluster-optimized execution.

---

## 🔍 Workflow Overview

The pipeline consists of the following steps:

1. **Quality Control & Trimming** – `fastp`  
2. **Host/DNA Filtering (optional)** – `xengsort` (run only for PDX samples)  
3. **Alignment** – `STAR`
4. **Alignment Metrics** – `Picard CollectAlignmentSummaryMetrics`  
5. **Quantification** – `RSEM`

**Inputs:**  
- FASTQ files (single-end or paired-end)  
- Human/mouse reference genome, annotations, and indexes (provided separately)

**Outputs:**  
- QC reports (`results/fastp/`, `results/picard/`)  
- Aligned BAM files (`results/star/`)  
- Gene- and transcript-level expression estimates (`results/rsem/`)  

---

## 🛠️ Software Stack

| Tool   | Purpose                                | Version (recommended) |
|--------|----------------------------------------|-----------------------|
| fastp  | FASTQ QC and adapter trimming          | 0.23.1                |
| xengsort | Host/DNA filtering (for PDX samples) | 2.0.9                 |
| STAR   | RNAseq read alignment                  | 2.7.3a                |
| RSEM   | Gene/transcript quantification         | 1.3.0                 |
| Picard | Alignment metrics & RNAseq QC          | 2.10.9                |
| Snakemake | Workflow engine                     | 6.15.3 (tested)       |

---

## ⚙️ Setup Instructions

### 1. Create Snakemake Environment

Start a build node session:
```bash
salloc --partition=build -c 1 -t 2:0:0 --mem 2G
```
Install Snakemake v6.15.3:
```bash
conda install -n base -c conda-forge mamba
mamba create -c conda-forge -c bioconda -n snakemake6153 snakemake=6.15.3
```
Activate the environment:
```bash
conda activate snakemake6153
```

### 2. Clone Workflow Repository

```bash
cd ~/workflows
git clone git@github.com:UHN-PMLB/RNAseq_PDX_PDO_PatientTissue_Snakemake_v202509.git
```
Clone the workflow into home directory first.

### 3. Project Directory Setup
#### 3.1 Start an interactive session

```bash
salloc -c 1 -t 2:0:0 --mem 2G
```
Move the cloned workflow into your working directory.

#### 3.2 Configure `config/samples.tsv`

| sample_name | sample_type    | single_pair_end | fq1                           | fq2                           |
|-------------|----------------|-----------------|-------------------------------|-------------------------------|
| SAMPLE001   | PDX            | pe              | /path/to/data/SAMPLE001_R1.fq | /path/to/data/SAMPLE001_R2.fq |
| SAMPLE002   | PDO            | pe              | /path/to/data/SAMPLE002_R1.fq | /path/to/data/SAMPLE002_R2.fq |
| SAMPLE003   | PatientTissue  | pe              | /path/to/data/SAMPLE003_R1.fq | /path/to/data/SAMPLE003_R2.fq |

- `sample_type` determines whether `xengsort` is run (`PDX = yes`, `PDO/PatientTissue = skipped`).
- `single_pair_end` should be `se` for `single-end` data, `pe` for `paired-end`.

#### 3.3 Configure `workflow/Snakefile`

Update the `workdir` parameter to point to your working directory.

#### 3.4 Reference Data (`ref/`)

The `ref/` directory contains genome FASTA, annotations (GTF), and prebuilt STAR/RSEM indexes.
Please contact the workflow maintainer to obtain and transfer the reference data to HPC.

---

## ▶️ Running the Workflow

### 1. Dry-run (test only, no jobs executed)

```bash
snakemake -n -p
```

### 2. Run locally with a few cores

```bash
snakemake --cores 4
```

### 3. Submit to SLURM

```bash
sbatch scheduler.sh
```

---

## 📂 Output Structure

- results/fastp/ – QC reports in HTML/JSON
- results/star/ – Aligned BAM files + STAR logs
- results/picard/ – Alignment metrics (Picard CollectAlignmentSummaryMetrics)
- results/rsem/ – Gene and transcript quantifications

---

## 📌 Notes

This workflow is optimized for HPC4Health Slurm. Adapt scheduler settings (scheduler.sh) for other clusters.

---

## 👤 Contact

For questions or support, please contact:
[Guanqiao Feng] – [guanqiao.feng@uhn.ca]
