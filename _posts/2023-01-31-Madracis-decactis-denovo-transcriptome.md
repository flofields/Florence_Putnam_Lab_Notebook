### *MDEC* transcriptome data files on URI andromeda:

Location on Andromeda, the HPC server for URI:

---
cd/data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes

MDEC_R1_001.fastq.gz
MDEC_R1_001.fastq.gz.md5
MDEC_R2_001.fastq.gz
MDEC_R2_001.fastq.gz.md5


All files were downloaded to andromeda URI HPC location

---
## 1) Run FastQC on MDEC_R1 and MDEC_R2

##### a) Make folders for raw FastQC results and scripts

```
cd /data/putnamlab/flofields/denovo_transcriptome/data
mkdir fastqc_results

cd /data/putnamlab/flofields/denovo_transcriptome
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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/denovo_transcriptome/data/raw/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results/
done
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc.sh

Submitted batch job 289531
```
---
## 2) Combined QC output into 1 file with MultiQC, do not need a script due to fast computational time
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results/*fastqc.zip -o /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results/multiqc/
```
---
## 3) Copy MultiQC and FastQC files to local computer: These lines of code should not be ran in the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results/multiqc/multiqc_report.html /Users/flo_f/Putnam-lab/bioinformatics/MDEC_transcriptome/original_fastqc
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results/*.html /Users/flo_f/Putnam-lab/bioinformatics/MDEC_transcriptome/original_fastqc
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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/trim.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/denovo_transcriptome/data/raw
module load fastp/0.19.7-foss-2018b

fastp --in1 MDEC_R1_001.fastq --int2 MDEC_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --trim_tail1 15 --trim_tail2 15 --out1 /data/putnamlab/flofields/denovo_transcriptome/data/trimmed/MDEC_001_trim_R1.fastq --out2 /data/putnamlab/flofields/denovo_transcriptome/data/trimmed/MDEC_001_trim_R2.fastq
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/trim.sh
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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc_trimmed.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/denovo_transcriptome/data/trimmed/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trimmed/
done
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc_trimmed.sh
Submitted Batch Job 293773
```
---
## 7) Run MultiQC on trimmed data, Combined QC output into 1 file with MultiQC
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2
multiqc /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trimmed/*fastqc.zip -o /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trimmed/trimmed_multiqc
```
---
## 8) Copy multiqc and fastqc to computer, use terminal window fro desktop not in server
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trimmed/trimmed_multiqc/multiqc_report.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trimmed_fastqc
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trimmed/*.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trimmed_fastqc
```
---
# Changing parameters for fastqc by just removing the adapter and trimming poly g tail

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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/trim2.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/denovo_transcriptome/data/raw
module load fastp/0.19.7-foss-2018b

fastp --in1 MDEC_R1_001.fastq --int2 MDEC_R2_001.fastq --detect_adapter_for_pe -D --trim_poly_g --out1 /data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R1.fastq --out2 /data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R2.fastq
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/trim2.sh
Submitted batch job 293922
```
---
## Repeated steps 5 through 8  
```
mkdir fastqc_results_trim2
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc_trim2.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/trim2
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC*
do
fastqc $file --outdir /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trim2/
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/fastqc_trim2.sh
Submitted batch job 294032
```
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2
multiqc /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trim2/*fastqc.zip -o /data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trim2/trim2_multiqc
```
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trim2/trim2_multiqc/multiqc_report.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trim2_fastqc
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/denovo_transcriptome/data/fastqc_results_trim2/*.html /Users/flo_f/OneDrive/Desktop/Putnam-lab/bioinformatics/MDEC_transcriptome/trim2_fastqc
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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/trinity.sh
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
#SBATCH -D /data/putnamlab/flofields/denovo_transcriptome/data/trim2
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/denovo_transcriptome/data/trim2

#Load Trinity module

module load Trinity/2.15.1-foss-2022a

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R1.fastq.gz \
--right \
/data/putnamlab/flofields/denovo_transcriptome/data/trim2/MDEC_001_trim2_R2.fastq.gz \
--CPU 36
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/trinity.sh
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

scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta /Users/flo_f/OneDrive/Desktop/Putnam-Lab/mdec-rnaseq

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
nano /data/putnamlab/flofields/denovo_transcriptome/scripts/busco.sh
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
[ -z "$query" ] && query="${labbase}/flofields/denovo_transcriptome/data/trim2/trinity_out_dir.Trinity.fasta" # set this to the query (genome/transcriptome) you are running
[ -z "$ff_to_compare" ] && ff_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o /data/putnamlab/flofields/denovo_transcriptome/data/busco/busco_output -m transcriptome

echo "STOP" $(date)
```
```
sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/busco.sh
Submitted batch job 309345
```
