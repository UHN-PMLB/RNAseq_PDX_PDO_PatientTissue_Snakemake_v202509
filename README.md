# RNAseq Snakemake Workflow

version202509

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

### 2. Clone workflow

Login build mode and clone the workflow to home directory
```
cd ~/workflows
git clone git@github.com:
```
