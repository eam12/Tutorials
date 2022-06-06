## Downloading FASTQ Files from NCBI's Short Read Archive (SRA)

Created June 2, 2022      

Created by Elizabeth Miller (millere@umn.edu) 

Basic steps:

- [Before starting](#Before-starting)
- [Log into MSI](#Log-into-MSI)
- [Download and install all required software](#Download-and-install-all-required-software)
- [Start interactive MSI job](#Start-interactive-MSI-job)
- [Create output directory](#Create-output-directory)
- [Create text file of SRR IDs](#Create-text-file-of-SRR-IDs)
- [Download FASTQ files](#Download-FASTQ-files)
- [Identify any errors](#Identify-any-errors)



- [Create text file of SRR IDs](#Create-text-file-of-SRR IDs)


<!-- toc -->



### Before starting

This tutorial assumes you have a basic understanding of command line, MSI, and Conda. The following tutorials are recommended in this order:

1. [Introduction to Command Line - Part 1](https://github.com/eam12/Tutorials/blob/660301e784f6f87a49dd4fa0f00609ec66d5ce93/Intro_to_Command_Line/Intro_to_Command_Line.md)
2. [Introduction Minnesota Supercomputing Institute (MSI)](https://www.msi.umn.edu/tutorials/introduction-minnesota-supercomputing-institute-msi)
3. [Introduction to Conda](https://github.com/eam12/Tutorials/blob/ca8a9a3a8500b50034bebfac257b329004dcb53f/Intro_to_Conda/Intro_to_Conda.md)

### Log into MSI

How this is done will depend on whether you're using Terminal or PuTTY.

### Download and install all required software

We'll use the [sra-tools](https://ncbi.github.com/sra-tools/) (version 2.11.0) commands `prefetch` to download the SRA files and `fasterq-dump` to convert the SRA files to FASTQ files. You can read more about these two commands on the [SRA Toolkit GitHub page](https://github.com/ncbi/sra-tools/wiki/08.-prefetch-and-fasterq-dump). We'll then use [pigz](https://zlib.net/pigz/) (version 2.6) to compress the resulting FASTQ files.  

Both the SRA Toolkit and pigz need to be downloaded and installed in your MSI account. We'll use [Conda](https://docs.conda.io/en/latest/) to do this:

```
# load python3 module
module load python3

# create sra-tools environment and install sra-tools v.2.11.0
conda create --name sra-tools --channel bioconda sra-tools

# add pigz v.2.6 to your environment
conda install --name sra-tools --channel anaconda pigz
```

### Start an interactive MSI job 

We need to request a resource allocation for interactive use. 

```
srun --time=3:00:00 -N 1 --ntasks-per-node=8 --mem-per-cpu=2gb --tmp=10g -p interactive --pty bash
```

We are requesting 3 hours of:

- 8 processors (cores)
- 2 gigabytes per core processor
- 10 gigabytes of temporary disk

Wait for the job to start.

### Create an output directory

We need a directory where all of our downloaded files will be put. You can name this directory anything you want, but try to use something descriptive. I'll call mine `fastq_raw`.

```
mkdir -p ~/project/fastq_raw
```

Now go to that newly created directory.

```
cd ~/project/fastq_raw
```

#### Create a text file of your SRR IDs

Create a text file listing all of the SRR IDs you want to download from the SRA, one ID per line. There are a number of ways to make text files, but we'll use the function `nano`. Again, you can call this file anything you want, but try to name it something descriptive.

```
nano SRRs_to_download.txt
```

Copy and paste your SRR IDs into the file. Here's an example:

<img src=https://github.com/JohnsonSingerLab/Salmonella-Surveillance/blob/master/images/SRRs_to_download.png  width="500">

Now exit (and save) the newly created text file using the `control` + `X` keys.

Lastly, check that the number of SRR IDs in `SRRs_to_download.txt` is correct.

```
cat SRRs_to_download.txt | wc -l
```

#### Load Conda environment

We're almost ready to start downloading, but first we need to load the Conda environment we created (`sra-tools`).

```
module load python3 && source activate sra-tools
```

#### Download FASTQ files

Now we can start downloading! The following is a for loop combined with an if else statement. It can be read as: "For every SRR listed in the file `SRRs_to_download.txt`, prefetch the SRA file. If the prefetch is successful, extract the FASTQ files and compress them. If the prefecth is not successful, add the SRR to the `SRR_fails.txt` file."

```
for accession in $(cat SRRs_to_download.txt); do

printf "\n  Working on: ${accession}\n\n"

# download SRA object
prefetch ${accession} --verify yes

if [ $? -eq 0 ]; then

# pull fastq files out
fasterq-dump --progress --split-spot --skip-technical --split-files --threads $SLURM_NTASKS --temp /scratch.local ${accession}/${accession}.sra 

# remove SRA object
rm -rf ${accession}

# compress fastq files
printf "\n  Compressing: ${accession} FASTQ files\n"
pigz ${accession}*.fastq

else

# remove SRA object
rm -rf ${accession}

printf "${accession}\n" >> SRR_fails.txt

fi

done
```

Let's break down the command into more detailed explanantions:

- `for accession in $(cat SRRs_to_download.txt); do`  "For every SRR listed in the file `SRRs_to_download.txt` do the following..."
- `printf "\n  Working on: ${accession}\n\n"` Prints the phrase "Working on:" followed by the SRR (indicated by the variable `${accession}`). Just a nice way to visually keep track of where the script is in the list of samples.
- `prefetch ${accession}` Download the indicated SRR from NCBI.
    -  `--verify yes` Check that the downloaded SRA file is valid.
- `if [ $? -eq 0 ]; then` If the previous command (i.e. `prefetch`) was successful, then do the following...
- `fasterq-dump` Convert the FASTQ file(s) from the SRA file.
    - `--progress` Show SRA -> FASTQ conversion progress.
    - `--split-spot` Split spots into reads
    - `--skip-technical` Download only biological reads (skip technical reads)
    - `--split-files` Write reads into different files; files will receive suffix corresponding to read number (R1 and R2)
    - `--threads $SLURM_NTASKS` Number of processor cores to use (in our case is 8)
    - `--temp /scratch.local` Where scratch storage is located
    - `${accession}/${accession}.sra` Name of the SRR SRA being processed
- `rm -rf ${accession}` Remove the SRA file (to save memory).
- `printf "\n  Compressing: ${accession} FASTQ files\n"` Prints the phrase "Compressing: SRR FASTQ files." Again, just a nice way to visually keep track of how things are progressing.
- `pigz ${accession}*.fastq` Compresses the FASTQ files into fastq.gz files. Again, doing this saves space. I've found that most downstream programs can handle fastq.gz files.
- `else` If the previous command (i.e. `prefetch`) was *NOT* successful, do the following...
- `rm -rf ${accession}` Remove the SRA file.
- `printf "${accession}\n" >> SRR_fails.txt` Add the problem SRR to the `SRR_fails.txt` file.
- `fi` End of if else statement
- `done` End of for loop


- `parallel -j $SLURM_NTASKS` Run the following command as *n* jobs in parallel. The variable `$SLURM_NTASKS` indicates the number of jobs, which in our case is 8.

#### Identify any errors

Take a look at the `SRR_fails.txt` file. Depending on what the issue is, samples listed here could still be downloaded. Sometimes there is an issue with the download of one or more of your SRR IDs. For example, the FASTQ files may not be publically available or aren't found in the SRA database. There may also be a poor or lost connection to the NCBI server. A few error examples:

* **Prefetch: Access Denied**: This means the sample has been uploaded to the SRA database, but the FASTQ files are not publically available. Any sample with this error message will not be included in downstream anlyses and should be removed from the `infantis_all_seq_samples.txt` and `infantis_all_seq_samples_usa.txt` files. I also like to update the `metadata_all_merged_final.txt` *Keep?* and *Issue* columns with this information. 
* **Prefetch: No Data**: This means that for whatever reason, there are no FASTq files for this sample in the SRA database. Any sample with this error message will not be included in downstream anlyses and should be removed from the `infantis_all_seq_samples.txt` and `infantis_all_seq_samples_usa.txt` files. I also like to update the `metadata_all_merged_final.txt` *Keep?* and *Issue* columns with this information.
* **Prefetch: Other Error**: Other Prefetch errors usually have to do with a poor or lost connection to the NCBI server. We will try downloading these samples again. You can look at the specific error for a sample by looking at the `prefetch_errors_other.txt` or `prefetch.out` file.
* **Fasterq-Dump or Unexplained**: Occasionally there could be an error with the `fasterq-dump` step. Additionally, for whatever reason, `prefetch` sometimes skips a sample. This does not show up as an error in the `prefetch.out` output file. We will try downloading these samples again.  

We can try downloading these SRRs again either via command line or by going directly to the SRA website and clicking the download link.

Example: 
```
prefetch SRR6491317 --verify yes
fasterq-dump --progress --split-spot --skip-technical --split-files --threads $SLURM_NTASKS --temp /scratch.local SRR6491317/SRR6491317.sra 
pigz SRR6491317*.fastq
```

#### Check your sample size

10) Once you truly have all possible sample FASTQ files downloaded, double check that the FASTQ file counts match the number of samples you will be using for downstream analyses:

```
# total number of SRRs we wanted
ls SRRs_to_download.txt | wc -l

# total number of SRRs evntually downloaded
ls *_1.fastq.gz | sed 's/_1.fastq.gz//g' | sort | uniq | wc -l

# total number of FASTQ files
ls *.fastq.gz | wc -l

# total number of R1 FASTQ files
ls *_1.fastq.gz | wc -l

# total number of R2 FASTQ files
ls *_2.fastq.gz | wc -l
```




