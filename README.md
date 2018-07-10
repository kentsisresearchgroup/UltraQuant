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
git clone https://github.com/kentsisresearchgroup/UltraQuant
```

## Configuration

To run UltraQuant, create a cope of the Snakefile "UltraQuant.sm". Adjust the "User Variables" section to run on your datasets.

### Directories

"WD" variable sets the path that will serve as the relative path for all analyses. The user should create a new directory for each separate analysis. In a cluster environment, this path should be accessible to all nodes on the network.

"TMP" variable should point to a local scratch storage directory that should be mounted locally on each node on a cluster, as opposed to a high-performance network storage location. MaxQuant performs its calculations in this directory. Since MaxQuant is very read/write-intensive, local disk access should result in faster running times. Else, user may specify another location.

### MaxQuant

MaxQuant requires mass spectrometry data as RAW files, search database as FASTA file, and a parameter file.

"DATABASE" variable points to the path of user-provided search database FASTA file.

"RAW" variable points to the directory containing all user-provided .raw mass spectrometry data files. UltraQuant will run MaxQuant on ALL .raw files in this directory.

"THREADS" variable specifies the number of computational threads used by MaxQuant.

"MQ", a non-user-specified variable, points to MaxQuantCmd.exe, which is provided in "/MaxQuant/bin". The version of MaxQuant used here is 1.6.2.3. License agreement for redistribution in located in "/MaxQuant". To switch to alternate versions of MaxQuant, please update both the executable and parameter file. 

"PAR", a non-user-specified variable, points to a default MaxQuant parameter file, which is provided in "/MaxQuant". This template is used to generate search parameters.

Note: The generated MaxQuant parameter file will contain default settings to MaxQuant. To alter them, you may create new MaxQuant parameter files by executing "/MaxQuant/bin/MaxQuantGui.exe" using Windows operating system. Click File > Load parameters... and load "/MaxQuant/mqpar_template.xml". Adjust the parameters. DO NOT add/remove RAW/FASTA files as this part will be filled in by UltraQuant. Finally, click File > Save parameters... and save it as "mqpar.xml" in the same directory as the template parameter file. UltraQuant will automatically detect this file and use it as input for MaxQuant instead of its self-generated parameters. Alternatively, you may edit the "PAR" variable to specify alternative search parameter xml files. 

## Running UltraQuant

This example demonstrates how to run UltraQuant on an [LSF](https://www.ibm.com/support/knowledgecenter/en/SSETD4/product_welcome_platform_lsf.html) cluster.

```bash
snakemake --snakefile UltraQuant.sm --cluster \
"bsub -J 'myjob.{params.J}' -n {params.n} -R {params.R} -W 16:00 -o 'myjob.{params.o}' -eo 'myjob.{params.eo}'" \
--jn {rulename}.{jobid}.sj -j 50 -k --latency-wait 60 --use-singularity --singularity-args "--bind /data:/data,/lila:/lila,/scratch:/scratch" --ri
```

"-J" specifies the job name.

"-n" specifies the number of requested threads.

"-R" specifies the resources provided for the the job. "span[host=1]" specifies the number of nodes used for parallelization, if implemented in specific cluster environments. "rusage[mem=15]" specifies the minimum memory in GB requested per node. "span[ptile=32]" specifies the number of requested processors used per node. MaxQuant runs faster when hyperthreaded on the same node. These values can be adjusted to fit specific cluster configurations.

"-o" specifies the output file name.

"-eo" specifies the error file name.

"-W" wall time argument might need to be adjusted to account for larger datasets used in MaxQuant.

"--bind" argument will need to be adjusted so that Singularity can access files outside the container. You must bind the directory for UltraQuant, "TMP", and "RAW" for MaxQuant so that UltraQuant can read and write data on your system. Else, there will be an error associated with singularity.

### Expected Output

UltraQuant will execute MaxQuant, which will generate result tables in the user-specified working directory, under "out/combined/txt/".