# RNAseq Snakemake Workflow

version 202509

## Workflow Overview

![worflow diagram](images/RNAseq_workflow-diagram.png)

RNAseq workflow using fastp, xengsort(optional), star, rsem for PDX, PDO, and patient tissue RNAseq data. Customized for HPC4Health Slurm cluster.

## Workflow Setup

### 1. Snakemake conda environment setup

Login build mode
```
salloc --partition=build -c 1 -t 2:0:0 --mem 2G
```
Install snakemake (v6.15.3) as a conda environment
```
conda install -n base -c conda-forge mamba
mamba create -c conda-forge -c bioconda -n snakemake6153 snakemake=6.15.3
```

### 2. Clone Workflow

Login build mode and clone the workflow to home directory
```
cd ~/workflows
git clone git@github.com:UHN-PMLB/RNAseq_PDX_PDO_PatientTissue_Snakemake_v202509.git
```
Move the copied workflow directory to your working directory

### 3. Project Directory Setup
#### 3.1 Login interactive mode
```
salloc -c 1 -t 2:0:0 --mem 2G
```
#### 3.2 Set up config/samples.tsv

| sample_name | sample_type    | single_pair_end | fq1                           | fq2                           |
|-------------|----------------|-----------------|-------------------------------|-------------------------------|
| SAMPLE001   | PDX            | pe              | /path/to/data/SAMPLE001_R1.fq | /path/to/data/SAMPLE001_R2.fq |
| SAMPLE002   | PDO            | pe              | /path/to/data/SAMPLE002_R1.fq | /path/to/data/SAMPLE002_R2.fq |
| SAMPLE003   | PatientTissue  | pe              | /path/to/data/SAMPLE003_R1.fq | /path/to/data/SAMPLE003_R2.fq |

*Note: `sample_type` decides if xengsort step will be skpped (PDO, PatientTissue) or not (PDX).

#### 3.3 Configure workflow/Snakefile

Update `workdir` to your working directory

#### 3.4 ref/

ref directory contains all the human and mouse reference genome, annotation and index. Please contact author to transfer the ref/ folder in HPC. 

### 4. Run the workflow

Activate snakemake environment
```
mamba activate snakemake6153
```
Run the workflow
```
sbatch scheduler.sh
```
