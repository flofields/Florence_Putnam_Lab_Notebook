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

Note: The reads being used are non-stranded and [paired](https://github.com/flofields/MDEC_Reference_Transcriptome/blob/main/metadata/30-818136646_R4%20(3).pdf). Because the read are not stranded a library type such as RF or FR will not be used.

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

**Danielle also got a high number (78.9%) of duplicated BUSCOs in her de novo transcriptome of Apulchra, but Kevin got much less duplication (6.9%) in his Past transcriptome assembly. Danielle aligned her transcriptome with the Apul Jill and Trinity worked on.**

**eukaryote contaminant sequences (ftp.ncbi.nlm.nih. gov/pub/kitts/contam_in_euks.fa.gz), NCBI viral (ref_ viruses_rep_genomes) and prokaryote (ftp.ncbi.nlm.nih.gov/pub/kitts/contam_in_prok.fa)**

---
<span style="color: red;">Note all Bioinfamatic work this far has been done in andormeda. I will now be using unity which is be reflected through subtle changes in the Sbatch scripts

Make a unity strach directory and folders to suit

    ws_allocate transcriptomes 30 -G pi_hputnam_uri_edu
    cd /scratch/workspace/ffields_uri_edu-transcriptomes
    mkdir mdec
    cd mdec
    mkdir data
    mkdir scripts
    cd scripts

### Create eukaryote filtering script
Note downloading the eukaryote sequences from NCBI 

    nano trinity_euk_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_euk_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_euk_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_euk_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Mdec/trim2
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0


    echo "Creating output directory: filter_euk_mdec" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec

    echo "Unzipping contaminant database" $(date)
    gunzip -c /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_euks.fa.gz \
      > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/contam_in_euks.fa

    echo "BLASTing fasta against eukaryote contaminant sequences" $(date)

    cd /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Mdec/trim2

    # Step 1: Run BLAST to identify contaminants
    blastn -query trinity_out_dir.Trinity.fasta \
           -subject /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/contam_in_euks.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/contaminant_hits_euks_trinity.txt

    echo "BLAST complete, filtering contaminant sequences" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/contaminant_hits_euks_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" trinity_out_dir.Trinity.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq trinity_out_dir.Trinity.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/cleaneuk_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_euk_mdec." $(date)

```
sbatch /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/mdec/trinity_euk_contam.sh

Submitted batch job 38657005
```
### download prokaryotic contaminant sequences and Create prokaryote filtering script
```
curl -o /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa https://ftp.ncbi.nlm.nih.gov/pub/kitts/contam_in_prok.fa

nano trinity_prok_contam.sh
```
    #!/bin/bash
    #SBATCH --job-name=filter_prok_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_euk_prok.out  # %j = job ID
    #SBATCH -e slurm-filter_euk_prok.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_euk_mdec" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec

    echo "BLASTing the fasta against prokaryote contaminant sequences" $(date)

    cd /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/

    # Step 1: Run BLAST to identify contaminants
    blastn -query cleaneuk_trinity_sequences.fasta \
           -subject /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/contaminant_hits_prok_trinity.txt

    echo "BLAST complete, filtering contaminant sequences" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/contaminant_hits_prok_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneuk_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq cleaneuk_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/cleaneukandprok_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_prok_mdec." $(date)

```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/mdec/trinity_prok_contam.sh
Submitted batch job 38665397
```

No contaninants were identified using this scriptso i decided to only do a blast to see if any prok contaminants were identified using the script below

    #!/bin/bash
    #SBATCH --job-name=filter_prok_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_prok_mdec2.out  # %j = job ID
    #SBATCH -e slurm-filter_prok_mdec2.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a

    cd /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/

    echo "converting the fa file to a db"

    makeblastdb -in /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa \
                -dbtype nucl \
                -out /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok_db

    echo "Blasting fasta against viral genomes to look for contaminants" $(date)

    blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_euk_mdec/cleaneuk_trinity_sequences.fasta \
           -db /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok_db \
           -out prok_contaminant_hits_rr.txt \
           -outfmt 6 \
           -evalue 1e-4 \
           -perc_identity 90
```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/blastn_prok.sh
Submitted batch job 38815569
```
It seems that they are no prok contanimants. moving on to viral contaminants

### Unity has a ref virus geneome from NCBI, I will be using ref genome to filter any sequences that are encoded for viral contaminants. The path is below Create a viral filtering script

/datasets/bio/ncbi-db/2025-06-22/

```
nano trinity_filter_viral_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_viral_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_viral_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_viral_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_viral_mdec" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec

echo "Run BLAST to identify any contaminants" $(date)

blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/cleaneukandprok_trinity_sequences.fasta \
       -db /datasets/bio/ncbi-db/2025-06-22/ref_viruses_rep_genomes \
       -out contaminant_hits_viral_trinity.txt \
       -outfmt 6 \
       -evalue 1e-4 \
       -perc_identity 90

# Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/contaminant_hits_viral_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral _mdec/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneukandprok_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/retained_ids.txt

             echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_prok_mdec/cleaneukandprok_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_viral_mdec." $(date)
```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/trinity_filter_viral_contam.sh
Submitted batch job 38815587

### Filitering sym sequences from the fasta file

Download symbiont files from [marine genomics](https://marinegenomics.oist.jp/symb/gallery) in designated folder and unzipped them

    curl -L -o symbB.v1.0.genome.fa.gz "https://marinegenomics.oist.jp/symb/download/symbB.v1.0.genome.fa.gz"
    curl -L -o symC_scaffold_40.fasta.gz "https://marinegenomics.oist.jp/symb/download/symC_scaffold_40.fasta.gz"
    curl -L -o symA3_37.fasta.gz "https://marinegenomics.oist.jp/symb/download/symA3_37.fasta.gz"

    gunzip symA3_37.fasta.gz
    gunzip symbB.v1.0.genome.fa.gz
    gunzip symC_scaffold_40.fasta.gz

Download symbiont files from NCBI using [accession numbers](https://static-content.springer.com/esm/art%3A10.1007%2Fs00248-021-01868-8/MediaObjects/248_2021_1868_MOESM1_ESM.pdf)

    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/AB704015.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=AB704015.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/AF333506.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=AF333506.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/KM816410.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=KM816410.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/EU139607.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=EU139607.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/AF180120.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=AF180120.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/AF333504.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=AF333504.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/KT149343.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=KT149343.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/KT149344.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=KT149344.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/KT149341.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=KT149341.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/MK024930.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=MK024930.1&rettype=fasta&retmode=text"
    curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/KT149340.1.fasta \
      "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=nuccore&id=KT149340.1&rettype=fasta&retmode=text"

Combined all symbiodiniaceae files so I can make a blast database (db)

cat /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/*.fasta > symbiodiniaceae_combined.fasta
makeblastdb -in symbiodiniaceae_combined.fasta -dbtype nucl -out symbiont_db_combined

#### Using the combined files and making a db with sbatch then filtering all the hit sequences

    nano trinity_filter_sym_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_sym_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_sym_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_sym_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating the db for the combined sym files"
    makeblastdb -in /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/symbiodiniaceae_combined.fasta \
            -dbtype nucl \
            -out /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/symbiont_db_combined

    echo "Run BLAST to identify any contaminants" $(date)

    blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta \
           -db /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/symbiont_db_combined
           -out contaminant_hits_sym_trinity.txt \
           -outfmt 6 \
           -evalue 1e-4 \
           -perc_identity 90

    # Step 2: Extract contaminant IDs from BLAST output
        awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_hits_sym_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_ids.txt

        # Step 3: Create list of sequences to KEEP (non-contaminants)
        grep "^>" clean_euk_prok_viral_trinity_sequences.fasta | sed 's/^>//' | sort \
        > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/all_sequence_ids.txt
        comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/all_sequence_ids.txt \
                 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_ids.txt \
                 > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/retained_ids.txt

                 echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/Filtered_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_sym_mdec." $(date)


sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/trinity_filter_sym_contam.sh
Submitted batch job 39137931

Recieved an error file

    Removing uri version main
    Loading uri version main
    NOTE: The modules under this branch will not run on the login node. Use
    --constraint=avx512 for sbatch or srun sessions. 
    Removing uri version main
    Loading uri version main
    NOTE: The modules under this branch will not run on the login node. Use
    --constraint=avx512 for sbatch or srun sessions. 
    FASTA-Reader: Ignoring invalid residues at position(s): On line 12: 1-3, 6, 8-10, 12-13, 18, 20-21, 23, 26-27, 29-30, 32, 34-36, 38-39, 42, 44-64, 66, 70-78, 80, 82-87
    FASTA-Reader: Ignoring invalid residues at position(s): On line 13: 1-3, 6, 8-10, 12-13, 18, 20-21, 23, 26-27, 29-30, 32, 34-36, 38-39, 42, 44-64, 66, 70-78, 80, 82-87
    FASTA-Reader: Ignoring invalid residues at position(s): On line 25: 1-3, 6, 8-10, 12-13, 18, 20-21, 23, 26-27, 29-30, 32, 34-36, 38-39, 42, 44-64, 66, 70-78, 80, 82-87
    FASTA-Reader: Ignoring invalid residues at position(s): On line 32: 1-3, 6, 8-10, 12-13, 18, 20-21, 23, 26-27, 29-30, 32, 34-36, 38-39, 42, 44-64, 66, 70-78, 80, 82-87
    FASTA-Reader: Ignoring invalid residues at position(s): On line 33: 1-3, 6, 8-10, 12-13, 18, 20-21, 23, 26-27, 29-30, 32, 34-36, 38-39, 42, 44-64, 66, 70-78, 80, 82-87
    /var/spool/slurm/slurmd/job39137931/slurm_script: line 25: -out: command not found
    awk: fatal: cannot open file `/scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_hits_sym_trinity.txt' for reading: No such file or directory
    grep: clean_euk_prok_viral_trinity_sequences.fasta: No such file or directory

I will first find lines that don't conform to proper FASTA then isolate the clean squences and remove the rest in an interactive job and then rebuild the db 

    grep -n -v -E '^>|\b[ACGTNacgtn]+\b' symbiodiniaceae_combined.fasta

    srun --nodes=1 --cpus-per-task=1 --mem=2G --time=00:30:00 --constraint=avx512 --pty bash
    module load uri/main seqtk/1.4-GCC-12.3.0
    seqtk seq -A symbiodiniaceae_combined.fasta > cleaned2_symbionts.fasta

Reruning the script below with the new fasta and including the entire dir for the out blast file

    #!/bin/bash
    #SBATCH --job-name=filter_sym_mdec
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_sym_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_sym_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating the db for the combined sym files"
    makeblastdb -in /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/cleaned2_symbionts.fasta \
            -dbtype nucl \
            -out /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/symbiont_db_combined

    echo "Run BLAST to identify any contaminants" $(date)

    blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta \
           -db /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/symbiont_dbs/symbiont_db_combined
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_hits_sym_trinity.txt \
           -outfmt 6 \
           -evalue 1e-4 \
           -perc_identity 90
   

    # Step 2: Extract contaminant IDs from BLAST output
        awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_hits_sym_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_ids.txt

        # Step 3: Create list of sequences to KEEP (non-contaminants)
        grep "^>" clean_euk_prok_viral_trinity_sequences.fasta | sed 's/^>//' | sort \
        > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/all_sequence_ids.txt
        comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/all_sequence_ids.txt \
                 /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/contaminant_ids.txt \
                 > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/retained_ids.txt

                 echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_sym_mdec/Filtered_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_sym_mdec." $(date)
 
 sbatch trinity_filter_sym_contam.sh
Submitted batch job 39198426

### Running BUSCO on filtered fasta and downloading metazoa db
curl -o /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_busco_mdec/metazoa_odb10.2020-09-10.tar.gz https://bioinformaticsonline.com/bookmarks/view/42621/busco-datasets/metazoa_odb10.2020-09-10.tar.gz 

tar -xzf metazoa_odb10.2020-09-10.tar.gz
busco -i Trinity_filtered.fasta -l metazoa_odb10 -m transcriptome -o busco_output 

nano filter_busco_mdec

#!/bin/bash
#SBATCH --job-name=filter_busco_mdec
#SBATCH --nodes=1
#SBATCH --cpus-per-task=15
#SBATCH --mem=200G
#SBATCH --time=24:00:00
#SBATCH -o slurm-filter_busco.out
#SBATCH -e slurm-filter_busco.err
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --mail-user=ffields@uri.edu
#SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_busco_mdec/
#SBATCH --constraint=avx512


echo “Creating output directory: busco analysis mdec” $(date)
mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_busco_mdec

echo “START” $(date)
#configure BUSCO ini file
busco --config /scratch/workspace/ffields_uri_edu-transcriptomes/myconfig.ini

#set your de novo transcriptome query file
query=“/scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_mdec/clean_euk_prok_viral_trinity_sequences.fasta”

#run busco with your config file against your db of interest
busco --config “/scratch/workspace/ffields_uri_edu-transcriptomes/myconfig.ini” -f -c 15 -i “${query}” -l metazoa_odb10 -o mdec_busco_output -m transcriptome
echo “STOP” $(date)

sbatch filter_busco_mdec
Submitted batch job 39135465

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

## From here down move to new md
Translate it to amino acids

    transdecoder.LongOrfs -t filtered_transcripts.fasta
    transdecoder.Predict -t filtered_transcripts.fasta

Format headers for Broccoli

    sed 's/^>/>Mdec|/' filtered_transcripts.fasta.transdecoder.pep > Mdec.faa

Organize multi-species input
Place all species FASTAs into one directory

broccoli.py -i /PATH/orthofiles/ \
            -o broccoli_output/ \
            -t 16
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/filter_busco_mdec.sh