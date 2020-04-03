# Remove Host DNA from Paired Reads Using Minimap2 and Samtools 

SanitizeMePaired is a GUI application (with an equivalent CLI) that takes paired fastq files, and removes reads mapping to a reference file from the paired fastq files. The most obvious use is to remove the host contaminants from metagenomic sequencing. The GUI version requires direct access to a linux computer or X11 forwarding.

![Image description](https://github.com/jiangweiyao/SanitizeMePaired/blob/master/SanitizeMePaired_Screenshot2.jpg)

It uses Minimap2 to map to the reference sequence and samtools to pull out the reads that did not map.

SanitizeMePaired depends on Python 3.6.9 and the Gooey and colored modules (for the GUI interface) as well as Minimap2 and Samtools. SanitizeMePaired includes a Conda recipe to install these environmental dependencies (Minimap2, Samtools, Python 3.6.9, and the Gooey/colored modules) in an easy and reproducible fashion using Miniconda/Anaconda.

A script for easy installation of Miniconda is included (install_miniconda.sh).

A script to install Miniconda, download the human_g1k_v37 reference file, and build the environment is also included (install_all.sh). 

## Summary - Installation 
1. Clone Repository 
2. Install Conda if not already in environment
3. Create conda environment
4. Acquire reference file - download the human_g1k_v37 reference file using the following command
```
wget ftp://ftp-trace.ncbi.nih.gov/1000genomes/ftp/technical/reference/human_g1k_v37.fasta.gz
```

## Summary - How to run after installation.
1. Activate conda environment - `conda activate SanitizeMePaired`
2. Run GUI version - `~/SanitizeMePaired/SanitizeMePaired_GUI.py`
3. Or run CLI version - `~/SanitizeMePaired/SanitizeMePaired_CLI.py -h`
4. Deactivate conda environment - `conda deactivate` when finished to protect your conda environment

## Clone this code using GIT

### Install git for Debian systems using the following command (if necessary)
```
sudo apt update
sudo apt install git
```

##Installation directions 
These instructions install the code into your home path. Change the instructions if appropriate. 

### Clone the code from repository
```
cd ~
git clone https://github.com/CDCgov/SanitizeMePaired
```

or from the latest development at
https://github.com/jiangweiyao/SanitizeMePaired

### Install Miniconda, Download human_g1k_v37, and build conda environment
You can do all three things by using the following command. Then, you can skip the next 3 sections.
```
. ~/SanitizeMePaired/install_all.sh
```

### Install Miniconda (if no Conda is install on system). 
You can run the prepackaged script install_miniconda.sh to install into your home directory (recommended) by using the following command
```
. ~/SanitizeMePaired/install_miniconda.sh
```

Detailed instruction on the the Miniconda website if anything goes wrong:
https://conda.io/projects/conda/en/latest/user-guide/install/linux.html

### Clone the environment. Need to do once.

We use conda to create an environment (that we can activate and deactivate) to install our dependent software and resolve their dependencies. This environment is called "SanitizeMePaired". The following command assumes your environment file is in your home path. Modify as appropriate.

```
conda env create -f ~/SanitizeMePaired/environment.yml
```

The command to generate the environment originally is in the included `SanitizeMePaired_MakeCondaEnv.txt` file. 

## Download Reference Files
The human_g1k_v37 reference file can be downloaded from the link below:
ftp://ftp-trace.ncbi.nih.gov/1000genomes/ftp/technical/reference/human_g1k_v37.fasta.gz
with the following command
```
wget ftp://ftp-trace.ncbi.nih.gov/1000genomes/ftp/technical/reference/human_g1k_v37.fasta.gz
```

You can use any fasta or fasta.gz format reference file for this tool.


## Run the code.

Activating your environment makes the software you installed in that environment available for use. You will see "(SanitizeMePaired)" in front of bash after activation.
```
conda activate SanitizeMePaired
```

Run the GUI version with the following command. A folder containing test fastq files is included in the SanitizeMePaired/test/ directory. The human_g1k_v37 reference file is downloaded by the `install_all.sh file`. Increase or decrease the number of threads to use as well as select your long read technology. Then, hit `Start` to run the program. 
```
~/SanitizeMePaired/SanitizeMePaired_GUI.py
```

Run the CLI version with the test files with the following command
```
 ~/SanitizeMePaired/SanitizeMePaired_CLI.py -i SanitizeMePaired/test/ -r ~/SanitizeMe/human_g1k_v37.fasta.gz -o ~/dehost_output/test_paired_CLI
```

Get help for the CLI version with the following command
```
~/SanitizeMePaired/SanitizeMePaired_CLI.py -h
```

CLI generic usage
```
~/SanitizeMePaired/SanitizeMePaired_CLI.py -i <input directory contain fastq> -r <reference fasta/fasta.gz> -o <output location>
```


When you are finished running the workflow, exit out of your environment by running `conda deactivate`. Deactivating your environment exits out of your current environment and protects it from being modified by other programs. You can build as many environments as you want and enter and exit out of them. Each environment is separate from each other to prevent version or dependency clashes. The author recommends using Conda/Bioconda to manage your dependencies.

## Reference Files
The human_g1k_v37 reference file from NCBI is downloaded by the `install_all.sh` file . It was downloaded from: 
ftp://ftp-trace.ncbi.nih.gov/1000genomes/ftp/technical/reference/human_g1k_v37.fasta.gz

You can use any fasta or fasta.gz format reference file for SanitizeMePaired. 

## What the code is actually doing

The code is running the following commands.

```
minimap2 -ax sr /home/jyao/SanitizeMe/human_g1k_v37.fasta.gz SanitizeMePaired/test/F3D0_S188_L001_R1_001.fastq SanitizeMePaired/test/F3D0_S188_L001_R2_001.fastq -t 4 > /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001.sam
samtools view -u -f 4 /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001.sam > /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered.sam
samtools bam2fq /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered.sam > /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered.fastq
cat /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered.fastq | grep '^@.*/1$' -A 3 --no-group-separator >/home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered_r1.fastq
cat /home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered.fastq | grep '^@.*/2$' -A 3 --no-group-separator >/home/jyao/dehost_output/test_paired_CLI/F3D0_S188_L001_R1_001_filtered_r2.fastq
```
## Author
Jiangwei Yao

## Related Documents
* [Disclaimer](DISCLAIMER.md)
* [Contribution Notice](CONTRIBUTING.md)
* [License](LICENSE)

