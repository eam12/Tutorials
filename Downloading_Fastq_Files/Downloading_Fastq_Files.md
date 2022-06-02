## Downloading FASTQ Files from NCBI's Short Read Archive (SRA)

Created June 2, 2022      

Created by Elizabeth Miller (millere@umn.edu) 

#### Log into MSI

How this is done will depend on whether you're using Terminal or PuTTY.

#### Download and install all required software

We'll use the [sra-tools](https://ncbi.github.com/sra-tools/) (version 2.11.0) commands `prefetch` (with [GNU Parallel](https://www.gnu.org/software/parallel/) to parallelize this step) to download the SRA files and `fasterq-dump` to convert the SRA files to FASTQ files. We'll then use [pigz](https://zlib.net/pigz/) (version 2.6) to compress the resulting FASTQ files. We'll use [Conda](https://docs.conda.io/en/latest/) to install all except for GNU Parallel which is available as an MSI module. 

Note: You can run through a quick tutorial about Conda [here](https://github.com/eam12/Tutorials/blob/ca8a9a3a8500b50034bebfac257b329004dcb53f/Intro_to_Conda/Intro_to_Conda.md).

```
# load python3 module
module load python3

# create sra-tools environment and install sra-tools v.2.11.0
conda create --name sra-tools --channel bioconda sra-tools

# add pigz v.2.6 to your environment
conda install --name sra-tools --channel anaconda pigz
```

#### Start an interactive job on MSI

WE need to request

#### Create an output directory

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

#### Load all required software

We're almost ready to start downloading, but first we need to load all of the required software. This includes the Conda environment `sra-tools` we created earlier and the MSI module `parallel`.

```
# load sra-tools conda environment
module load python3 && source activate sra-tools

# load parallel module
module load parallel
```

#### Download SRA files

Now we can start downloading! 

```
cat SRRs_to_download.txt | parallel -j $PBS_NP "prefetch {} --verify yes --output-directory ~/infantis_data/fastq_raw" |& tee --ignore-interrupts prefetch.out
```

Let's break down the command:

`cat SRRs_to_download.txt` 

`parallel -j $PBS_NP`

`"prefetch {} --verify yes --output-directory ~/infantis_data/fastq_raw"`

`|& tee --ignore-interrupts prefetch.out`




#### Identify any download errors

```
# identify prefetch errors
# list of successful downloads
grep ": 1) .* was downloaded successfully" prefetch.out | sed -E "s/.*'(.*)'.*/\1/" > prefetch_success_SRRs.txt

# list of failed downloads
grep -v -f prefetch_success_SRRs.txt ~/infantis_data/metadata/SRRs_to_download.txt > prefetch_fails_SRRs.txt

# create files of specific prefetch errors
grep -f prefetch_fails_SRRs.txt prefetch.out > prefetch_errors.txt

# access denied error
grep "Access denied" prefetch_errors.txt > prefetch_errors_accessDenied.txt

# no data error
grep "no data ( 404 )" prefetch_errors.txt > prefetch_errors_noData.txt

# all other errors
grep -v -f <(cat prefetch_errors_accessDenied.txt prefetch_errors_noData.txt) prefetch_errors.txt > prefetch_errors_other.txt
```


#### Convert SRA files to compressed FASTQ files

```
for srr in $(cat prefetch_success_SRRs.txt); do
echo "Starting ${srr}..." | tee --ignore-interrupts --append fastqdump.out
fasterq-dump $srr --split-files --skip-technical --threads $PBS_NP --outdir ~/infantis_data/fastq_raw |& tee --ignore-interrupts --append fastqdump.out
# gzip file
pigz ${srr}*.fastq
done
```







































5) Create a fasterq_dump PBS job script. I keep all of my PBS job scripts in one directory (`~/pbs`), but you can put it wherever you want.

```
nano ~/pbs/fasterq_dump.pbs
```

`prefetch` options: 

* `--verify`: Verify download was successful
* `--output-directory`: path to output directory

`parallel` options:

* `-j`: Run *n* jobs in parallel

`fasterq-dump` options: 

* `--skip-technical`; download only biological reads (skip technical reads)
* `--split-files`: write reads into different files; files will receive suffix corresponding to read number (R1 and R2)
* `--threads`: Number of threads the command should use (use the PBS pre-set variable, `$PBS_NP`, which specifies the number of threads available within the job).
* `--outdir`: path to output directory

Paste the following into `fasterq_dump.pbs`, again obviously changing things to your own MSI account paths and email: 

```
#!/bin/bash -l   
#PBS -l nodes=1:ppn=8,walltime=24:00:00
#PBS -e /home/johnsont/millere/pbs/logs/fasterq_dump_$PBS_JOBID.err
#PBS -o /home/johnsont/millere/pbs/logs/fasterq_dump_$PBS_JOBID.out
#PBS -m abe
#PBS -M millere@umn.edu
#PBS -A johnsont
#PBS -N fasterq_dump

# load sra-tools conda environment
module load python3
source activate sra-tools

# load parallel module
module load parallel

# create directory where raw FASTQ files will be downloaded
mkdir -p ~/infantis_data/fastq_raw

# go to newly created fastq_raw directory
cd ~/infantis_data/fastq_raw

# download SRA files with prefetch
cat ~/infantis_data/metadata/SRRs_to_download.txt | parallel -j $PBS_NP "prefetch {} --verify yes --output-directory ~/infantis_data/fastq_raw" |& tee --ignore-interrupts prefetch.out
# can also use vdb-validate to verify that SRA file downloaded correctly

# identify prefetch errors
# list of successful downloads
grep ": 1) .* was downloaded successfully" prefetch.out | sed -E "s/.*'(.*)'.*/\1/" > prefetch_success_SRRs.txt

# list of failed downloads
grep -v -f prefetch_success_SRRs.txt ~/infantis_data/metadata/SRRs_to_download.txt > prefetch_fails_SRRs.txt

# create files of specific prefetch errors
grep -f prefetch_fails_SRRs.txt prefetch.out > prefetch_errors.txt

# access denied error
grep "Access denied" prefetch_errors.txt > prefetch_errors_accessDenied.txt

# no data error
grep "no data ( 404 )" prefetch_errors.txt > prefetch_errors_noData.txt

# all other errors
grep -v -f <(cat prefetch_errors_accessDenied.txt prefetch_errors_noData.txt) prefetch_errors.txt > prefetch_errors_other.txt

# convert SRA files to FASTQ
for srr in $(cat prefetch_success_SRRs.txt); do
echo "Starting ${srr}..." | tee --ignore-interrupts --append fastqdump.out
fasterq-dump $srr --split-files --skip-technical --threads $PBS_NP --outdir ~/infantis_data/fastq_raw |& tee --ignore-interrupts --append fastqdump.out

# gzip file
pigz ${srr}*.fastq

done
```

* I always specify my error (`.err`) and output (`.out`) file paths in the PBS job script header. I keep them within the `~/pbs` directory in a directory called `logs`. Again, you can put these files wherever you want.
* Using 8 threads, it took ~XXX hours to download 8550 SRRs (2 FASTQ files [R1 and R2] per sample) for the serovar Infantis. Use this info to make a reasonable estimate for your own number of samples. Just make sure you give yourself *plenty* of extra time. It's always safest to request more time than you will actually use. If your walltime is exceeded before the job is finished running, you will either have to run it again from the beginning or figure out where it left off running and start it from there.

<img src=https://github.com/JohnsonSingerLab/Salmonella-Surveillance/blob/master/images/fasterq_dump.pbs.png  width="500">

6) Submit the fasterq_dump PBS job script. Remember, only use the `vetbiosci` node if you're waiting long periods of time in the queue for standard MSI resources. 

```
qsub ~/pbs/fasterq_dump.pbs
```

Make sure you take note of the job ID number. This will be helpful to have if you have to cancel the job or look at the error or output files. I also like to record the start and end times of all of my jobs. It gives me an idea of how much time I need to request in the future.

7) You should get an email when the fasterq_dump job has finished running. Check that the exit_status=0. If exit_status equals anything other than 0, [there may be a problem with your job](https://www.msi.umn.edu/support/faq/what-does-job-exit-status-xx-mean). Specifically, if the exit_status=123, there was likely an issue with downloading one or more of your FASTQ files. This is not unusual as the connection to NCBI isn't always great. In the next step we'll be doing some checking to figure out what samples we need to try and re-download. 

8) Create a list of the SRR FASTQ files that were not downloaded. We'll do this by first creating a list of all the FASTQ files downloaded and then comparing that list to the lists of "problem" SRRs that we identified in the fasterq_dump PBS job script (i.e. `prefetch_errors_accessDenied.txt`, `prefetch_errors_noData.txt`, and `prefetch_errors_other.txt`).

```
# go to directory containing downloaded FASTQ files
cd ~/infantis_data/fastq_raw

# create a list of FASTQs missing
for srr in $(cat ../metadata/SRRs_to_download.txt); do
    if [ ! -f "${srr}_1.fastq.gz" ]; then
        echo "${srr}_1.fastq.gz does not exist." >> missing_fastqs.txt
    fi
    if [ ! -f "${srr}_2.fastq.gz" ]; then
        echo "${srr}_2.fastq.gz does not exist." >> missing_fastqs.txt
    fi
done

# create list of SRRs missing FASTQ files
sed 's/\_.*$//' missing_fastqs.txt | sort | uniq > missing_SRRs.txt

# count number of missing SRRs
cat missing_SRRs.txt | wc -l
## XXXX missing samples

# create a file of what each missing SRR error is
for srr in $(cat missing_SRRs.txt); do
    if grep -q $srr prefetch_errors_accessDenied.txt
    then
        echo -e $srr'\t'"Prefetch: Access Denied" >> missing_SRRs_errorSummary.txt
    elif grep -q $srr prefetch_errors_noData.txt 
    then
        echo -e $srr'\t'"Prefetch: No Data" >> missing_SRRs_errorSummary.txt
    elif grep -q $srr prefetch_errors_other.txt 
    then
        echo -e $srr'\t'"Prefetch: Other Error" >> missing_SRRs_errorSummary.txt
    else
        echo -e $srr'\t'"Fasterq-Dump or Unexplained" >> missing_SRRs_errorSummary.txt
    fi
done
```

9) Download the newly created `missing_SRRs_errorSummary.txt` using FileZilla and view in Excel. There are four potential error categories a sample could fall into:

* **Prefetch: Access Denied**: This means the sample has been uploaded to the SRA database, but the FASTQ files are not publically available. Any sample with this error message will not be included in downstream anlyses and should be removed from the `infantis_all_seq_samples.txt` and `infantis_all_seq_samples_usa.txt` files. I also like to update the `metadata_all_merged_final.txt` *Keep?* and *Issue* columns with this information. 
* **Prefetch: No Data**: This means that for whatever reason, there are no FASTq files for this sample in the SRA database. Any sample with this error message will not be included in downstream anlyses and should be removed from the `infantis_all_seq_samples.txt` and `infantis_all_seq_samples_usa.txt` files. I also like to update the `metadata_all_merged_final.txt` *Keep?* and *Issue* columns with this information.
* **Prefetch: Other Error**: Other Prefetch errors usually have to do with a poor or lost connection to the NCBI server. We will try downloading these samples again. You can look at the specific error for a sample by looking at the `prefetch_errors_other.txt` or `prefetch.out` file.
* **Fasterq-Dump or Unexplained**: Occasionally there could be an error with the `fasterq-dump` step. Additionally, for whatever reason, `prefetch` sometimes skips a sample. This does not show up as an error in the `prefetch.out` output file. We will try downloading these samples again.

If you have no samples to try re-downloading (those with "Prefetch: Other Error" or "Fasterq-Dump or Unexplained" errors), you're ready to move on to Step 10. However, more often than not, there will be some to re-try. Unless you have a lot of samples to try re-downloading, the easiest way to do this is to download samples one-by-one. For example:

```
# start interactive job
qsub -I -l nodes=1:ppn=8,walltime=04:00:00

# load sra-tools conda environment
module load python3
source activate sra-tools

# load parallel module
module load parallel

# go to directory where raw FASTQ files will be downloaded
cd ~/sinfantis_data/fastq_raw

# use prefetch to download the SRA file
prefetch SRR6491317 --verify yes --output-directory ~/infantis_data/fastq_raw
## the file downloaded successfully if it says "'SRR6491317' was downloaded successfully"

# convert the SRA file to FASTQ files
fasterq-dump SRR6491317 --split-files --skip-technical --threads $PBS_NP --outdir ~/infantis_data/fastq_raw

# check that both FASTQ files were created
ls SRR6491317*

# gzip the FASTQ files
pigz SRR6491317*.fastq
```

If you have a lot of samples to try, you could re-run the fasterq_dump PBS job with an updated list of SRRS to download (e.g. `SRRs_to_download_round2.txt`). Keep in mind that unless you also update all of the output file names in the in the script (e.g. `prefetch.out`, `prefetch_errors_accessDenied.txt`, etc.), running the fasterq_dump PBS job again will overwrite the files you alrady created.

If you are still having trouble downloading a specific sample, it's best to look the SRR up in the [NCBI's SRA database](https://www.ncbi.nlm.nih.gov/sra). If everything looks like it shoul dbe working, you can try downloading directly from the [NCBI's SRA Run Browser](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=run_browser): 

<img src=https://github.com/JohnsonSingerLab/Salmonella-Surveillance/blob/master/images/SRA_Download.png>

10) Once you truly have all possible sample FASTQ files downloaded, double check that the file counts match the number of samples you will be using for downstream analyses:

```
# go to directory where raw FASTQ files are downloaded
cd ~/infantis_data/fastq_raw

# total number of FASTQ files
ls *.fastq.gz | wc -l

# total number of R1 FASTQ files
ls *_1.fastq.gz | wc -l

# total number of R2 FASTQ files
ls *_2.fastq.gz | wc -l

# total number of samples
ls *_1.fastq.gz | sed 's/_1.fastq.gz//g' | sort | uniq | wc -l
```




