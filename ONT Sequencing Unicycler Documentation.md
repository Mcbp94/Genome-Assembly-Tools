# ONT Sequencing Unicycler Documentation

Mark Chan

*12 August 2020*

## 1. Installing Unicycler through `conda`

```shell
# Login to nscc04 from an NTU login node
ssh nscc04-ib0
```

#### 1.0 Installing `conda`

If `miniconda` has not been previously installed, visit [this link](https://docs.conda.io/en/latest/miniconda.html) to download the relevant installer. Right click on download link > copy link address.

```shell
# Download miniconda
wget <paste link>

# Install miniconda
bash Miniconda3-latest-Linux-x86_64.sh

# Check for successful installation
conda list
```

If `miniconda` is installed and working, `conda list` will display a list of installed packages and their versions. **Note the python version**. The current version of Unicycler is v0.4.8 and it cannot run on python 3.8 or higher.

```shell
# Search for available python versions
conda search python

# To downgrade/upgrade python version
conda install python=3.7.0
# or whatever available version you want
```

#### 1.1 Installing Unicycler

```shell
# Download and install Unicycler
conda install -c bioconda unicycler

# Check for successful installation by viewing help information
unicycler --help
# or checking Unicycler version
unicycler --version
```

If connection is lost temporarily, repeat the installation process. The current version of Unicycler is the same as the one available on [Galaxy](https://usegalaxy.org/) server (v0.4.8).

## 2. Importing Sequencing Data

The current working directory for this ONT run analysis is located at `/home/users/ntu/mark0026/scratch/ont/aw34-19p/`

#### 2.0 Useful commands to know

```shell
# Go to a working directory
cd scratch
cd ont
cd aw34-19p
# or
cd scratch/ont/aw34-19p
# to return to a higher working directory
cd ..
cd ..
# or 
cd ../..
# to return to highest working directory
cd ~

# Creating a directory
mkdir <directory name>

# List all files in a directory
ls
# List details of all files in a directory
ll

# Print working directory
pwd

# Copying mergedpassed.fastq from fastqpass folder to flyeanalysis folder in the same directory
cp fastqpass/mergedpassed.fastq flyeanalysis/mergedpass.fastq
cp <original location/file name> <destination file/file name>

# Wildcard
*
# Selecting all fastq files
*.fastq
# Merging all fastq files in a directory into a new fastq file
cat *.fastq > mergedfiles.fastq
```

#### 2.1 FTP transfer

```shell
# Go to working directory
cd scratch/ont/aw34-19p

# Create new directories in aw34-19p
mkdir fastqpass
mkdir fastqfail+pass
```

The aim of this was to compare the assemblies using combined Illumina short reads with passed vs failed+passed reads.

#### 2.2 Upload files using FileZilla

1. Install and setup FileZilla for direct upload from computer from [https://ﬁlezilla-project.org/](https://ﬁlezilla-project.org/) 

2. Login to the server using FileZilla Client.

   Host: mark0026@ntu.nscc.sg

   Username: mark0026

   Password: H0!ys14r5

   Port: 22

3. Drag-and-drop **Illumina AW34-19p `R1.gz` and `R2.gz` into `aw34-19p` **directory, as well as **ONT passed and failed `*.fastq`** ﬁles into their respective `fastq` directories.

Note that `.gz` compressed fastq files can be used directly by Unicycler. There is no need to decompress the files. Using zipped files will allow faster upload time into the server.

#### 2.3 Preparing Unicycler input files

Merging `fastq` files in `fastqpass` directory.

```shell
# Open fastqpass directory and check for successful upload of fastq files
cd fastqpass
ls

# Merging all fastq files
cat *.fastq > mergedpass.fastq

# Create new directory to submit to nscc
cd ..
mkdir unicyclerpass

# Copy mergedpass.fastq into NSCC submission directory
cp fastqpass/mergedpass.fastq unicyclerpass/mergedpass.fastq
```

Copy Illumina `R1.fastq` and `R2.fastq` into NSCC submission directory.

```shell
cp R1.gz R2.gz unicyclerpass/
```

 Check for all files in `unicyclerpass` directory:

```shell
cd unicyclerpass
ls	
```

The `unicyclerpass` directory should contain 3 files:

```shell
R1.gz
R2.gz
mergedpass.fastq
```

#### 2.4 Running Unicycler

Create `unicycler.pbs` PBS script using nano text editor:

```shell
nano unicycler.pbs
```

PBS script `unicycler.pbs` for submission:

```shell
#!/bin/bash
#PBS -q normal
#PBS -P Personal
#PBS -l select=1:ncpus=24:mem=64gb
#PBS -l walltime=12:00:00

export TMPDIR=/home/users/ntu/mark0026/scratch/

cd ${PBS_O_WORKDIR}

unicycler -1 R1.gz -2 R2.gz -l mergedpass.fastq --threads 24 -o aw34-19p_output
```

Press `Ctrl+X` and `y` to exit and save the file. The output files will be saved in a newly created`aw34-14p_output` directory within the `unicyclerpass` directory.

Submit the script as a job into NSCC queue.

```shell
qsub unicycler.pbs
```

Check on the status of the job submission

```shell
qstat -x
```

Which gives something like this:

![image-20200814172719012](C:\Users\markc\AppData\Roaming\Typora\typora-user-images\image-20200814172719012.png)

#### 2.5 Viewing output files

Go to the directory of the output files

```shell
cd aw34-19p_output
```

View the list of files in the directory

```shell
ll
```

You will see the following files:

![image-20200814173402223](C:\Users\markc\AppData\Roaming\Typora\typora-user-images\image-20200814173402223.png)

View the output log file:

```shell
less unicycler.log
```

The output files can be downloaded into the local computer terminal using FileZilla for further assessment and viewing. The `assembly.gfa` assembly graph file can be visualized using [Bandage software](https://rrwick.github.io/Bandage/).

*Note: Repeated the same for `fastqfail+pass` files.*

![image-20200814175440128](C:\Users\markc\AppData\Roaming\Typora\typora-user-images\image-20200814175440128.png)



