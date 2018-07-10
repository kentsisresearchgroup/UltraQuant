# UltraQuant

## Introduction

UltraQuant is an open framework for running MaxQuant on Linux computer clusters written in the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system.

## Installation

### Dependencies

UltraQuant uses [Miniconda](https://conda.io/miniconda.html) to install the [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow management system. The following dependencies are either included as part of UltraQuant or automatically installed using [Singularity](https://singularity.lbl.gov/) container to be used in diverse Linux environments:

* [mono](https://www.mono-project.com/)
* [MaxQuant](http://www.coxdocs.org/doku.php?id=maxquant:start)

### Installing UltraQuant

UltraQuant can be installed using git clone:

```bash
git clone https://github.com/kentsisresearchgroup/UltraQuant
```

## Configuration

To run UltraQuant, create a copy of the Snakefile "UltraQuant.sm" and adjust the "User Variables" section.

### Directories

"WD" sets the path that will serve as the relative path for all analyses. The user should create a new directory for each dataset that contains mass spectrometry data files. In a cluster environment, this path should be accessible to all nodes.

"TMP" specifies the directory which MaxQuant uses for writing and reading temporary files. This can be a local scratch storage directory that is mounted locally on each node on a cluster. Alternatively this can be a directory on a high-performance network storage file system, if network access is sufficiently fast. 

### MaxQuant

MaxQuant requires mass spectrometry data as RAW files, search database as FASTA file, and a parameter file.

"DATABASE" specifies the user-provided search database FASTA file.

"RAW" specifies the directory containing all user-provided .raw mass spectrometry data files. UltraQuant will run MaxQuant on all .raw files in this directory. To perform calculations on independent raw files, they should be organized in individual directories. 

"THREADS" specifies the number of computational threads used by MaxQuant.

"MQ" specifies the MaxQuant executable file, which is provided in "/MaxQuant/bin/MaxQuantCmd.exe" as part of this container distribution. The currently provided version of MaxQuant is 1.6.2.3, including its license agreement for redistribution located in "/MaxQuant". To switch to alternate versions of MaxQuant, please update both the executable and the parameter file. 

"PAR" specifies the template MaxQuant parameter file, which is provided in "/MaxQuant" and is used to generate specific search parameters, including file names, locations, as well as mass spectrum processing and analysis parameters.

To alter the mass spectrum processing and analysis parameters, you should create a new MaxQuant parameter file by executing "/MaxQuant/bin/MaxQuantGui.exe" using Windows operating system. Click File > Load parameters... and load "/MaxQuant/mqpar_template.xml" and adjust the parameters. DO NOT add or remove RAW and FASTA files as they are specified by UltraQuant. Finally, click File > Save parameters... and save it as "mqpar.xml" in the same directory as the template MaxQuant parameter file. UltraQuant will automatically detect this file and use it as input for MaxQuant instead of its self-generated parameters. Alternatively, you may edit the "PAR" variable to specify alternative search parameter xml files. 

## Running UltraQuant

This example demonstrates how to run UltraQuant on an [LSF](https://www.ibm.com/support/knowledgecenter/en/SSETD4/product_welcome_platform_lsf.html) cluster.

```bash
snakemake --snakefile UltraQuant.sm --cluster \
"bsub -J 'myjob.{params.J}' -n {params.n} -R {params.R} -W 16:00 -o 'myjob.{params.o}' -eo 'myjob.{params.eo}'" \
--jn {rulename}.{jobid}.sj -j 50 -k --latency-wait 60 --use-singularity --singularity-args "
--bind /data:/data,/lila:/lila,/scratch:/scratch" --ri
```

"-J" specifies the job name.

"-n" specifies the number of requested calculation threads.

"-R" specifies the resources necessary for the the job. "span[host=1]" specifies the number of nodes used for parallelization, if implemented in specific cluster environments. "rusage[mem=15]" specifies the minimum memory in GB requested per node. "span[ptile=32]" specifies the number of requested processors used per node. MaxQuant runs faster when hyperthreaded on the same node. These values can be adjusted to fit specific cluster configurations.

"-o" specifies the output file name.

"-eo" specifies the error file name.

"-W" specific the maximum time allowed for the job in hours.

"--bind" implements binding of directories for UltraQuant to access files outside of this Singularity container, including the directories for UltraQuant itself, "TMP" calculation directory, and "RAW" mass spectrometry files directory. 

### Expected Output

UltraQuant will execute MaxQuant, which will generate result tables in the user-specified working directory, under "out/combined/txt/".

### Authors

* Zining Chen | zc98@cornell.edu
* Paolo Cifani | cifanip@mskcc.org
* John Poirier | poirierj@mskcc.org
* Alex Kentsis | kentsisresearchgroup@gmail.com


