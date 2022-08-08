# Installing Flye (v 2.8.1)

by Mark Chan

14 September 2020

Flye v 2.8.1 [github](https://github.com/fenderglass/Flye)

------

### 1. Installing Flye

You can get the latest stable release of Flye through `Bioconda`:

```shell
# Installing Flye
conda instll flye

# Check for successful installation
flye -h, --help
```

Alternatively, you can get the released version from its GitHub releases page.

### 2. Running Flye

* Inputs can be in FASTA or FASTQ format, compressed or uncompressed with gz. However do note that Flye was primarily developed to run on raw reads.

* Flye currently supports Pacbio (raw, corrected, HiFi) and ONT (raw, corrected) reads.

* Expected error rates are <30% for raw, <3% for corrected, and <1% for HiFi.

##### 2.1 Create working directory for Flye analysis:

```shell
# Go to desired directory
cd scratch/ont/aw34-19p

# Create new directory 'flyeanalysis' for flye
mkdir flyeanalysis
```

##### 2.2 Uploading files using FileZilla

Drag and drop desired `fastq` files into the `flyeanalysis` directory.

```shell
# Open flyeanalysis directory and check for successful upload of fastq files
cd flyeanalysis
ls

# Merging all fastq files and check
cat *.fastq > mergedpass.fastq
less mergedpass.fastq
```

##### 2.3 Create `.pbs` script for submission to NSCC

Creating the `.pbs` script in the flyeanalysis directory:

```shell
nano flyeanalysis.pbs
```

PBS script `flyeanalysis.pbs` for submission:

```shell
#!/bin/bash
#PBS -q normal
#PBS -P Personal
#PBS -l select=1:ncpus=24:mem=64gb
#PBS -l walltime=12:00:00

export TMPDIR=/home/users/ntu/mark0026/scratch/tmp

cd ${PBS_O_WORKDIR}

flye --nano-raw sequence.fastq --out-dir sequence
```

Press `Ctrl+X` and `y` to exit and save the file. The output files will be saved in a newly created `out_nano` directory within the `flyeanalysis` directory.

##### 2.4 Submitting job into NSCC

Submit the script as a job into NSCC queue:

```shell
qsub flyeanalysis.pbs
```

Check on the status of the job submission:

```shell
qstat
```

##### 2.5 Viewing output files

The output files can be downloaded into the local computer terminal using FileZilla for further assessment and viewing. The `assembly_graph.gfa` assembly graph file can be visualized using [Bandage software](https://rrwick.github.io/Bandage/).

<img src="C:\Users\markc\OneDrive - Nanyang Technological University\PhD\Experiments\Sequencing\202008 ONT Run\200805 ONT Run\Flye\out_nano\assembly.png" alt="assembly" style="zoom:50%;" />

Figure 1. Bandage visualization of *Burkholderia thailandensis* AW34-19p assembly graph file. Using Flye, Chromosome 1 and Chromosome 2 can be resolved using only long read sequencing (ONT).