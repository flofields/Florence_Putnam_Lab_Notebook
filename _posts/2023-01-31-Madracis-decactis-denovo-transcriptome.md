---
LAYOUT: post
TITLE: Workflow for Madracis decactis de novo transcriptome
CATEGORY: [ de novo transcriptome, RNA seq ]
TAG: [ Madracis decactis, de novo transcriptome ]
---
## Designing a workflow to create a de novo transcriptome for *Madracis decactis*

**About**: This post details the steps taken to create a de novo transcriptome for *Madracis decactis* using samples obtained in August 2022 as part of the ENCORE project in the TPC-PI experiment. 

### *MDEC* transcriptome data files on URI andromeda:

Location on Andromeda, the HPC server for URI:

```
cd/data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes
```

```
MDEC_R1_001.fastq.gz
MDEC_R1_001.fastq.gz.md5
MDEC_R2_001.fastq.gz
MDEC_R2_001.fastq.gz.md5
```

All files were downloaded to andromeda URI HPC location

Copied all data files to new location on Andromeda

```
cp -r /data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes/MDEC* /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw

MDEC_R1_001.fastq.gz
MDEC_R1_001.fastq.gz.md5
MDEC_R2_001.fastq.gz
MDEC_R2_001.fastq.gz.md5
```

---
## 1) Run FastQC on MDEC_R1 and MDEC_R2

##### a) Make folders for raw FastQC results and scripts

```
cd /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data
mkdir fastqc_results

cd /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome
mkdir scripts
```

##### b) Unziped fastq files
```
gzip -d *.gz #files should change from fastq.gz to fastq
```

##### c) Removed .md5 files #these files checks any file or sequence file downloaded to ensure they match one placed into the HPC server
```
rm MDEC_R1_001.fastq.gz.md5
rm MDEC_R2_001.fastq.gz.md5
```

##### d) Checked latest module
```
module -avail #shows the lastest module release to be used
```

##### e) Write script for checking quality with FastQC and submit as job on Andromeda
```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc.sh
```
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc.sh

Submitted batch job 289531
```
---
## 2) Combined QC output into 1 file with MultiQC, do not need a script due to fast computational time
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results/multiqc/
```
---
## 3) Copy MultiQC and FastQC files to local computer: These lines of code should not be ran in the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results/multiqc/multiqc_report.html /Users/flo_f/Putnam-lab/bioinformatics/MDEC_transcriptome/original_fastqc
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results/*.html /Users/flo_f/Putnam-lab/bioinformatics/MDEC_transcriptome/original_fastqc
```
---
### [Output MultiQC Report](https://github.com/flofields/MDEC_Reference_Transcriptome/tree/c02b9093942749497552c2e5e5018301b1b9f231/FastQC_Reports/Original)
---
### Understanding a [MultiQC Report](https://nf-co.re/eager/2.5.0/docs/output#multiqc-report) and [Fastp](https://github.com/OpenGene/fastp#adapters)
### [Secondary Fastp source](https://open.bioqueue.org/home/knowledge/showKnowledge/sig/fastp)

## 4) Trim and clean reads

##### a) Make trimmed reads folder in data file
```
mkdir data/trimmed
cd trimmed
```

##### b) Write script for Trimming and run on Andromeda

Run fastq on files

Trims 15bp from the 3'R1 and 5'R2 end of reads

Trims poly G tail if present

```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trim.sh
```
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw
module load fastp/0.19.7-foss-2018b

<<<<<<< HEAD
fastp --in1 MDEC_R1_001.fastq --in2 MDEC_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --trim_tail1 15 --trim_tail2 15 --out1 /data/putnamlab/flofields/denovo_transcriptome/data/trimmed/MDEC_001_trim_R1.fastq --out2 /data/putnamlab/flofields/denovo_transcriptome/data/trimmed/MDEC_001_trim_R2.fastq
=======
fastp --in1 MDEC_R1_001.fastq --int2 MDEC_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --trim_tail1 15 --trim_tail2 15 --out1 /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trimmed/MDEC_001_trim_R1.fastq --out2 /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trimmed/MDEC_001_trim_R2.fastq
>>>>>>> 4897777390d15fd11d27d78bc3417b18290e65b0
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trim.sh
Submitted batch job 290641
```
---

## 5) Check Quality of trimmed files

##### a) Check number of trimmed files
```
ls -1 | wc -l
#2
```
##### b) Check number of reads in /trimmed directory and in /raw directory. When checking the number of reads make sure you are in the correct directory for both the raw and trimmed reads.

###### look at raw reads
to do this you need to use the code below to determine the name of the sequence
```
less (filename)
eg. less MDEC_001_trim2_R2.fastqq
```
```
zgrep -c "@A01587"MDEC* > seq_counts

MDEC_R1_001.fastq:223590727
MDEC_R2_001.fastq:223590727
```
###### look at trimmed reads
###### the & alllows for the line of code to run i the background
###### "nohup" allows for the process to keep running if you close the terminal or change the current directory
```
nohup zgrep -c "@A01587"MDEC* > trimmed_seq_counts &
15516- job number
```

## 6)Run Fastq on trimmed reads
```
mkdir fastqc_results_trimmed
```
```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc_trimmed.sh
```
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trimmed/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trimmed/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc_trimmed.sh
Submitted Batch Job 293773
```
---
## 7) Run MultiQC on trimmed data, Combined QC output into 1 file with MultiQC
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2
multiqc /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trimmed/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trimmed/trimmed_multiqc
```
---
## 8) Copy multiqc and fastqc to computer, use terminal window from desktop not in server
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trimmed/trimmed_multiqc/multiqc_report.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trimmed_fastqc
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trimmed/*.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trimmed_fastqc
```
---
# Changing parameters for fastqc by just removing the adapter and trimming poly g tail

The removal of bp from the tail of both reads was not necessary if the adapter was being trimmed.

#### a) Trim and clean reads

##### i) Make trimmed reads folder in data file
```
mkdir data/trim2
cd trim2
```

##### ii) Write script for Trimming and run on Andromeda

Run fastq on files

Turn on poly G tail trimming

```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trim2.sh
```
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw
module load fastp/0.19.7-foss-2018b

<<<<<<< HEAD
fastp --in1 MDEC_R1_001.fastq --in2 MDEC_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --out1 /data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R1.fastq --out2 /data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R2.fastq
=======
fastp --in1 MDEC_R1_001.fastq --int2 MDEC_R2_001.fastq --detect_adapter_for_pe -D --trim_poly_g --out1 /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/MDEC_001_trim2_R1.fastq --out2 /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/MDEC_001_trim2_R2.fastq
>>>>>>> 4897777390d15fd11d27d78bc3417b18290e65b0
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trim2.sh
Submitted batch job 293922
```
---
## Repeated steps 5 through 8  
```
mkdir fastqc_results_trim2
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc_trim2.sh
```
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trim2/
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/fastqc_trim2.sh
Submitted batch job 294032
```
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2
multiqc /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trim2/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trim2/trim2_multiqc
```
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trim2/trim2_multiqc/multiqc_report.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trim2_fastqc
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/fastqc_results_trim2/*.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trim2_fastqc
```
## 9) Run Trinity with forward and reverse sequences
[Documentation on steps to running trinity](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Running-Trinity#strand_specific_assembly)
[Danielle Becker Pollinksi's Notebook Post](https://github.com/daniellembecker/DanielleBecker_Lab_Notebook/blob/master/_posts/2023-08-31-Acropora-pulchra-denovo-transcriptome.md) was also used a source.

Before creating a script to run trinity determine whether the reads are parired or unparied to select the library type

Note: The reads being used are non-stranded and [paired](https://github.com/flofields/MDEC_Reference_Transcriptome/blob/main/metadata/30-818136646_R4%20(3).pdf) therefore the library type RF and FR will be used.


seqType :fq - for Fastq format
max_memory :100G = max memory for trinity 100G should not be changed, per communications with the developer
CPU 36 = the number of CPUs should match the amount your computing cluster has access to, for andromeda the maximum number of cores is 36

#### a) Write Trinity Script
```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trinity.sh
```
```
#!/bin/bash
#SBATCH --job-name=20240213_trinity
#SBATCH --time=30-00:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --exclusive
#SBATCH --export=NONE
#SBATCH --mem=500GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2

#Load Trinity module

module load Trinity/2.15.1-foss-2022a

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/MDEC_001_trim2_R1.fastq.gz \
--right \
/data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/MDEC_001_trim2_R2.fastq.gz \
--CPU 36
```
```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/trinity.sh
Submitted batch job 304139
```
```
squeue -u ffields
```
Trinity completed successfully!!!! When Trinity completes, it creates a 'trinity_out_dir.Trinity.fasta' output file (or prefix based on the output directory you specify).

Trinity groups transcripts into clusters based on shared sequence content. Such a transcript cluster is very loosely referred to as a 'gene'. This information is encoded in the Trinity fasta accession. An example Fasta entry for one of the transcripts is formatted like so:

```
>TRINITY_DN100465_c0_g1_i1 len=518 path=[0:0-517]
TAACCGATTACACGACGAACAGAGTGTAATAGAGACATCAGGAAGAAGGGACCGATAGTA
CTGATATTTCAGTGGAGGAGGCCACTTCTTTACCATTACCTCACGGCTGTTCATGGTCTG
CAATGTTGCCCGACCCACGCGGACTGGAACGAACTCAGATCCACCTTGCTCAAAGCTCAT
TAGCTTGGCAGTGAAAGGATCCTCTTCCTCTTCTTCGGGGGGTTCATCTGTCAACTGCAG
GGACTGAAAGTTGCCCATGTCTCTTTCCTGCCAGTTCGAGTGCTTCTTTTCTCGACCCCT
TGGCGGCTCTACCTCGATCAATTTCAAAGCATCCTCGTCACTACAAAAACAGTGATATTT
AGTTAGCACAAATCCTGTGTCACGCCATCTCACGTCACGCTTTCTTCTGTCACGCTACTT
CAATTACCTGATGCCATCCTCCAAGACAAACTCGACCAGCGGGAGTACCTCGAAAGATGA
GAATGAATAGACAAATGGCTGTCGACAGTTGATACACT
```
Download Trinity fasta to Desktop if needed, too large to have stored there always

scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta /Users/flo_f/OneDrive/Desktop/Putnam-Lab/mdec-rnaseq

## Assessing assembly quality
Use [Trinity toolkit utilities](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Transcriptome-Contig-Nx-and-ExN50-stats) for a assembly quality assessment

#### a) Run TrinityStats.pl script for the stats output, the path of the trinity module needs to be used and then add the util folder before you can access the .pl script

This script will compute the contig Nx statistics (eg. the contig N50 value), in addition to a modification of the Nx statistic that takes into consideration transcript expression (read support) data, which we call the ExN50 statistic.

Based on the lengths of the assembled transcriptome contigs, we can compute the conventional Nx length statistic, such that at least x% of the assembled transcript nucleotides are found in contigs that are at least of Nx length. The traditional method is computing N50, such that at least half of all assembled bases are in transcript contigs of at least the N50 length value.
```
/opt/software/Trinity/2.15.1-foss-2022a/trinityrnaseq-v2.15.1/util/TrinityStats.pl trinity_out_dir.Trinity.fasta > trinity_assembly_stats
```
Trinity_assembly stata output 
```
################################
## Counts of transcripts, etc.
################################
Total trinity 'genes':  468945
Total trinity transcripts:      752474
Percent GC: 42.90

########################################
Stats based on ALL transcript contigs:
########################################

        Contig N10: 4386
        Contig N20: 2940
        Contig N30: 2181
        Contig N40: 1653
        Contig N50: 1227

        Median contig length: 420
        Average contig: 761.95
        Total assembled bases: 573346771
```

The N10 through N50 values are shown computed based on all assembled contigs. In this example, 10% of the assembled bases are found in transcript contigs at least 3,786 bases in length (N10 value), and the N50 value indicates that at least half the assembled bases are found in contigs that are at least 1,227 bases in length.

The contig N50 values can often be exaggerated due to an assembly program generating too many transcript isoforms, especially for the longer transcripts. To mitigate this effect, the script will also compute the Nx values based on using only the single longest isoform per 'gene':
```qq
#####################################################
## Stats based on ONLY LONGEST ISOFORM per 'GENE':
#####################################################

        Contig N10: 3786
        Contig N20: 2476
        Contig N30: 1798
        Contig N40: 1309
        Contig N50: 924

        Median contig length: 362
        Average contig: 636.26
        Total assembled bases: 298370046
```

You can see that the Nx values based on the single longest isoform per gene are lower than the Nx stats based on all assembled contigs, as expected, and even though the Nx statistic is really not a reliable indicator of the quality of a transcriptome assembly, the Nx value based on using the longest isoform per gene is perhaps better for reasons described above.

## 10) Run Busco (Benchmarking Universal Single-Copy Orthologs)

- Commands and overview for running BUSCO here: https://busco.ezlab.org/busco_userguide.html

- uses highly conserved single-copy orthologs; evolutionary informed expectations of gene content.

- appears that you can focus a BUSCO analysis to orthologs related to your target taxa.

- below shows a BUSCO analysis comparing the crayfish targetted for the novo transcriptome assembly to 44 other arthropod species assemblies and a single vertebrate assembly:

[Theissinger et al. 2016](https://www.sciencedirect.com/science/article/abs/pii/S1874778716300137) 
Citation: Theissinger, K., Falckenhayn, C., Blande, D., Toljamo, A., Gutekunst, J., Makkonen, J., ... & Kokko, H. (2016). De Novo assembly and annotation of the freshwater crayfish Astacus astacus transcriptome. Marine Genomics, 28, 7-10.

#### a) Make run-busco-transcriptome.sh script
```
nano /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/busco.sh
```
```
#!/bin/bash
#SBATCH --job-name="busco"
#SBATCH --time="100:00:00"
#SBATCH --nodes 1 --ntasks-per-node=20
#SBATCH --mem=250G
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
##SBATCH --account=putnamlab
##SBATCH --output="busco-%u-%x-%j"
##SBATCH --export=NONE

echo "START" $(date)

labbase=/data/putnamlab
busco_shared="${labbase}/shared/busco"
[ -z "$query" ] && query="${labbase}/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta" # set this to the query (genome/transcriptome) you are running
[ -z "$ff_to_compare" ] && ff_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_output -m transcriptome

echo "STOP" $(date)
```

```
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/busco.sh
```
Submitted batch job 309345 on March 18th 2024
Finished March 19th 2024

The run in BUSCO failed, I checked the busco_154851.log found in the directory below. It seems there was an error with the output path.
```
cd /data/putnamlab
```
```
2024-03-19 00:43:52 INFO:busco.ConfigManager    Configuring BUSCO with /opt/software/BUSCO/5.2.2-foss-2020b/config/config.ini
2024-03-19 00:43:52 INFO:busco.BuscoConfig      Mode is transcriptome
2024-03-19 00:43:52 ERROR:busco.BuscoRunner     Please do not provide a full path in --out parameter, no slash. Use out_path in the config.ini file to specify the full path.
2024-03-19 00:43:52 DEBUG:busco.BuscoRunner     Please do not provide a full path in --out parameter, no slash. Use out_path in the config.ini file to specify the full path.
Traceback (most recent call last):
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/run_BUSCO.py", line 103, in run
    self.load_config()
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/run_BUSCO.py", line 94, in load_config
    self.config_manager.load_busco_config_main(sys.argv)
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/BuscoLogger.py", line 62, in wrapped_func
    self.retval = func(*args, **kwargs)
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/ConfigManager.py", line 56, in load_busco_config_main
    self.config_main.validate()
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/BuscoConfig.py", line 407, in validate
    self._cleanup_config()
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/BuscoConfig.py", line 644, in _cleanup_config
    self._check_out_value()
  File "/opt/software/BUSCO/5.2.2-foss-2020b/lib/python3.8/site-packages/busco/BuscoConfig.py", line 592, in _check_out_value
    raise BatchFatalError(
busco.Exceptions.BatchFatalError: Please do not provide a full path in --out parameter, no slash. Use out_path in the config.ini file to specify the full path.
2024-03-19 00:43:52 ERROR:busco.BuscoRunner     BUSCO analysis failed !
2024-03-19 00:43:52 ERROR:busco.BuscoRunner     Check the logs, read the user guide (https://busco.ezlab.org/busco_userguide.html), and check the BUSCO issue board on https://gitlab.com/ezlab/busco/issues
```
<<<<<<< HEAD
In this new script I changed /data/putnamlab/flofields/denovo_transcriptome/data/busco/busco_output to busco_output
```
#!/bin/bash
#SBATCH --job-name="busco"
#SBATCH --time="100:00:00"
#SBATCH --nodes 1 --ntasks-per-node=20
#SBATCH --mem=250G
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
##SBATCH --account=putnamlab
##SBATCH --output="busco-%u-%x-%j"
##SBATCH --export=NONE

echo "START" $(date)

labbase=/data/putnamlab
busco_shared="${labbase}/shared/busco"
[ -z "$query" ] && query="${labbase}/flofields/denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta" # set this to the query (genome/transcriptome) you are running
[ -z "$ff_to_compare" ] && ff_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o busco_output -m transcriptome

echo "STOP" $(date)
```

```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/busco.sh
Submitted batch job 309636 on March 19th 2024
```

Failed again
Changes to the script 
```
[ -z "$ff_to_compare" ] && ff_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10" 

#to 

[ -z "$db_to_compare" ] && db_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"
```

Script used.
=======
In this new script I changed /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_output to busco_oput
>>>>>>> 4897777390d15fd11d27d78bc3417b18290e65b0
```
#!/bin/bash
#SBATCH --job-name="busco"
#SBATCH --time="100:00:00"
#SBATCH --nodes 1 --ntasks-per-node=20
#SBATCH --mem=250G
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
##SBATCH --account=putnamlab
##SBATCH --output="busco-%u-%x-%j"
##SBATCH --export=NONE

echo "START" $(date)

labbase=/data/putnamlab
busco_shared="${labbase}/shared/busco"
[ -z "$query" ] && query="${labbase}/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta" # set this to the query (genome/transcriptome) you are running
[ -z "$db_to_compare" ] && db_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o busco_output -m transcriptome

echo "STOP" $(date)
```

```
<<<<<<< HEAD
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/busco.sh
=======
sbatch /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/scripts/busco.sh
Submitted batch job 309636 on March 19th 2024
```

Failed again
Changes to the script made
```
[ -z "$ff_to_compare" ] && ff_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10" 

#to 

[ -z "$db_to_compare" ] && db_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

>>>>>>> 4897777390d15fd11d27d78bc3417b18290e65b0
Submitted batch job 309639 on March 19th 2024
```

Job failed again. I checked the output file which states that permission was denied for the directory '/glfs/brick01/gv0/putnamlab/busco_downloads/file_versions.tsv'
Checked permissions using the code below 

```
ls -l /glfs/brick01/gv0/putnamlab/busco_downloads/file_versions.tsv
```

Recieved output below then consulted jill who had permissions for this folder and was able to give me permission to access the folder

```
ls -l /glfs/brick01/gv0/putnamlab/busco_downloads/file_versions.tsv
-rw-r--r--. 1 jillashey putnamlab 18129 Mar 11 12:52 /glfs/brick01/gv0/putnamlab/busco_downloads/file_versions.tsv
```

Jill used this code below to give everyone in the group access to the folder

```
chmod ugo+rwx *
```
Submitted batch job 309641 on March 19th 2024

The busco_154851.log, busco_136325.log, busco_138450.log, busco_137567.log files where in the putnamlab folder. Moved them to /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs
The script output files slurm-309345.out, slurm-309639.out, slurm-309636.out, slurm-309641.out where found in the flofields script folder. Moved them to /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/slurm_output
#### The busco_output file was found in /data/putnamlab and was moved to /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/

The entire busco dir was manually made.
```
mkdir /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/
mkdir /data/putnamlab/flofields//ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs
mkdir /data/putnamlab/flofields//ENCORE_MDEC_denovo_transcriptome/data/busco/slurm-output
```
```
mv busco_154851.log /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs
mv busco_136325.log /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs
mv busco_138450.log /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs (file to be located)
mv busco_137567.log /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/busco_logs
```
```
mv slurm-309345.out /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/slurm-output
mv slurm-309639.out /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/slurm-output
mv slurm-309636.out /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/slurm-output
mv slurm-309641.out /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco/slurm-output

#slurm-output folder did not exsit so this path was incorrect, as a result when I ran it did generate a single slurm-output file that had the completed busco slurm-output results.
```
```
mv busco_output /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco
```

### BUSCO Resuts

```
# BUSCO version is: 5.2.2
# The lineage dataset is: metazoa_odb10 (Creation date: 2024-01-08, number of genomes: 65, number of BUSCOs: 954)
# Summarized benchmarking in BUSCO notation for file /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta
# BUSCO was run in mode: transcriptome

        ***** Results: *****

        C:98.5%[S:13.6%,D:84.9%],F:0.6%,M:0.9%,n:954
        940     Complete BUSCOs (C)
        130     Complete and single-copy BUSCOs (S)
        810     Complete and duplicated BUSCOs (D)
        6       Fragmented BUSCOs (F)
        8       Missing BUSCOs (M)
        954     Total BUSCO groups searched

Dependencies and versions:
        hmmsearch: 3.3
        metaeuk: GITDIR-NOTFOUND
```

BUSCO completeness looks great. Completeness looks for the presence or absence of highly conserved genes in an assembly. The aim is to have the highest percentage of genes identified in your assembly, a BUSCO complete score above 95% is considered good.
The complete and duplicated BUSCOs are high. Transcriptomes and protein sets that are not filtered for isoforms will lead to a high proportion of duplicates. So, next step suggestions:

Filter for isoforms
Map to closest genome
Filter symbiont genes to check if it helps duplication
