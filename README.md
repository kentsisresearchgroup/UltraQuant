# UltraQuant

## Introduction

UltraQuant is an open framework for running MaxQuant on a Linux cluster setting written in the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system.

## Installation

### Dependencies

UltraQuant requires [Miniconda](https://conda.io/miniconda.html) to install the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system. The following dependencies are either included as part of UltraQuant repo or automatically containerized with [Singularity](https://singularity.lbl.gov/) to run in almost any Linux production environment:

* [mono](https://www.mono-project.com/)
* [MaxQuant](http://www.coxdocs.org/doku.php?id=maxquant:start)

### Installing UltraQuant

UltraQuant is installed to your preferred computing environment using git clone:

```bash
git clone https://github.com/ziningchen/UltraQuant
```

## Configuration

To run UltraQuant, edit the Snakefile "UltraQuant.sm". Adjust the "User Variables" section to run on your datasets.

### Directories

"WD" variable sets the path that will serve as the relative path for all analyses. In a cluster environment, this path should be accessible to all nodes on the network.

"TMP" variable should point to a local scratch storage directory that should be mounted locally to each node on a cluster, as opposed to a high performance network storage location. MaxQuant performs its calculations in this directory. Since MaxQuant is very read/write-intensive, local disk access should result in faster running times. Else, user may specify for another location.

### MaxQuant

MaxQuant requires a database FASTA file, raw MS datasets, and a parameter file.

"DATABASE" variable points to the path of user-provided database FASTA file.

"RAW" variable points to the directory containing all user-provided .raw MS files.

"THREADS" variable specifies the number of threads used for MaxQuant analysis.

"MQ", a non-user-specified variable, points to MaxQuantCmd.exe, which is provided in "/MaxQuant/bin". This version of MaxQuant used is 1.6.2.3. You must use this version because the MaxQuant parameters file generated alters between versions. License agreement for redistribution in located in "/MaxQuant".

"PAR", a non-user-specified variable, points to a template MaxQuant parameters file, which is provided in "/MaxQuant". This file is used to generate the actual parameters file used in MaxQuant.

Note: The generated MaxQuant parameters file will contain default settings to MaxQuant. To change these, you may create your own MaxQuant parameters file by executing "/MaxQuant/bin/MaxQuantGui.exe" on a Windows system. Click File > Load parameters... and load "/MaxQuant/mqpar_template.xml". Adjust the parameters. DO NOT add/remove RAW/FASTA files as this part will be filled in by UltraQuant. Finally, click File > Save parameters... and save it as "mqpar.xml" in the same directory as the template parameters. UltraQuant will automatically detect this file and use it as input for MaxQuant instead of its self-generated parameters.


## Running UltraQuant

The below example demonstrates how to run UltraQuant on an [LSF](https://www.ibm.com/support/knowledgecenter/en/SSETD4/product_welcome_platform_lsf.html) cluster head node from within screen in case the connection to the head node is lost.

```bash
screen -S UQ
snakemake --snakefile UltraQuant.sm --cluster \
"bsub -J {params.J} -n {params.n} -R {params.R} -W 16:00 -o {params.o} -eo {params.eo}" \
--jn {rulename}.{jobid}.sj -j 50 -k --latency-wait 60 --use-singularity --singularity-args "--bind /data:/data,/lila:/lila,/scratch:/scratch" --ri
```

"-W" wall time argument might need to be adjusted to account for larger datasets used in MaxQuant.

"--bind" argument will need to be adjusted so that Singularity can access files outside the container. You must bind the directory for UltraQuant, "TMP", and "RAW" for MaxQuant so that UltraQuant can read and write data on your system. Else, there will be an error associated with singularity.

### Expected Output

UltraQuant will generate all tables outputted by MaxQuant in the user-specified working directory, under "out/combined/txt/".
