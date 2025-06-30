---
LAYOUT: post
TITLE: Workflow for Diploria labyrinthiformis de novo transcriptome
CATEGORY: [ de novo transcriptome, RNA seq ]
TAG: [ Diploria labyrinthiformis, de novo transcriptome ]
---
## Designing a workflow to create a de novo transcriptome for *Diploria labyrinthiformis*

**About**: This post details the steps taken to create a de novo transcriptome for *Diploria labyrinthiformis* using samples obtained in August 2022 as part of the ENCORE project in the TPC-PI experiment. 

### *Dlab* transcriptome data files on URI andromeda:

Location on Andromeda, the HPC server for URI:

```
cd/data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes
```

```
DLAB_R1_001.fastq.gz
DLAB_R1_001.fastq.gz.md5
DLAB_R2_001.fastq.gz
DLAB_R2_001.fastq.gz.md5
```

All files were downloaded to andromeda URI HPC location

Created directory for fastq files in home folder names flofields

```
mkdir ENCORE_Dlab_denovo_transcriptome
cd ENCORE_Dlab_denovo_transcriptome
mkdir data
cd data
mkdir raw
```

Copied all data files to new location on Andromeda

```
cp -r /data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes/DLAB* /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw

DLAB_R1_001.fastq.gz
DLAB_R1_001.fastq.gz.md5
DLAB_R2_001.fastq.gz
DLAB_R2_001.fastq.gz.md5
```

---
## 1) Run FastQC on MDEC_R1 and MDEC_R2

##### a) Make folders for raw FastQC results and scripts

```
cd /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data
mkdir fastqc_results

cd /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome
mkdir scripts
```

##### b) Unziped fastq files
```
gzip -d *.gz #files should change from fastq.gz to fastq
```

##### c) Removed .md5 files #these files checks any file or sequence file downloaded to ensure they match one placed into the HPC server
```
rm DLAB_R1_001.fastq.gz.md5
rm DLAB_R2_001.fastq.gz.md5
```

##### d) Checked latest module
```
module -avail #shows the lastest module release to be used
```

##### e) Write script for checking quality with FastQC and submit as job on Andromeda
```
nano /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/fastqc.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw/DLAB*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/fastqc.sh

Submitted batch job 350913
Date of Submission 20241126
```

Job failed so the script error was checked
```
cd /flofields/ENCORE_Dlab_denovo_transcriptome/data/raw/nano script_error

Specified output directory '/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/' does not e$
```
This issue is that there is no directory named fastqc_results which is where the script instructs the results to be placed._

```
mkdir fastqc_results
```
```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/fastqc.sh

Submitted batch job 351022
Date of Submission 20241126
```
---
## 2) Combined QC output into 1 file with MultiQC, do not need a script due to fast computational time
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/multiqc
```
---
## 3) Copy MultiQC and FastQC files to local computer: These lines of code should not be ran in the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/multiqc /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/DLAB_Reference_Transcriptome/data/fastqc_results
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/DLAB_Reference_Transcriptome/data/fastqc_results
```
---
### [Output MultiQC Report](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/DLAB_Reference_Transcriptome/data/fastqc_results_raw/multiqc/multiqc_report.html)
---
### Understanding a [MultiQC Report](https://nf-co.re/eager/2.5.0/docs/output#multiqc-report) and [Fastp](https://github.com/OpenGene/fastp#adapters)
### [Secondary Fastp source](https://open.bioqueue.org/home/knowledge/showKnowledge/sig/fastp)

## 4) Trim and clean reads

##### a) Make trimmed reads folder in data file

```
mkdir /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed
```


### For a more detailed look at the QC steps see [HERE](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/_posts/2024-12-10-QC-of-RNAseq-files-for-Dlab-ENCORE-2022-TPC-project.md)

##### b) Write script for Trimming and run on Andromeda

Run fastq on files to check the fastp.html report to look at the trimming information

Trims adapter
Trims poly G tail if present

```
nano /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/trim.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw

module load fastp/0.19.7-foss-2018b

fastp --in1 DLAB_R1_001.fastq --in2 DLAB_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --out1 /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/DLAB_001_trim_R1.fastq --out2 /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/DLAB_001_trim_R2.fastq
```
```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/trim.sh
```
#### Submitted job 353852
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
# The less command allows you to open the intended file file 
less (filename)
eg. less DLAB_001_trim_R2.fastq
```
```
zgrep -c "@A01587"DLAB* > seq_counts

MDEC_R1_001.fastq:223590727
MDEC_R2_001.fastq:223590727
```
###### look at trimmed reads
###### the & alllows for the line of code to run i the background
###### "nohup" allows for the process to keep running if you close the terminal or change the current directory
```
nohup zgrep -c "@A01587"DLAB* > trimmed_seq_counts &
15516- job number
```

## 6)Run Fastq on trimmed reads
```
mkdir /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed
```
```
nano /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/fastqc_trimmed.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/DLAB*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/fastqc_trimmed.sh
```
Submitted Batch Job 354776

---
## 7) Run MultiQC on trimmed data, Combined QC output into 1 file with MultiQC

```
#load module 
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed
```
---
## 8) Copy multiqc and fastqc to computer, use terminal window from desktop not in server
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/DLAB_Reference_Transcriptome/data/fastqc_results_trimmed
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/fastqc_results_trimmed/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/DLAB_Reference_Transcriptome/data/fastqc_results_trimmed
```
---

## 9) Run Trinity with forward and reverse sequences
[Documentation on steps to running trinity](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Running-Trinity#strand_specific_assembly)
[Danielle Becker Pollinksi's Notebook Post](https://github.com/daniellembecker/DanielleBecker_Lab_Notebook/blob/master/_posts/2023-08-31-Acropora-pulchra-denovo-transcriptome.md) was also used a source.

Before creating a script to run trinity determine whether the reads are parired or unparied to select the library type

Note: The reads being used are non-stranded and [paired](https://github.com/flofields/ENCORE_Transcriptomes/tree/ac61b292970cacdfe6ba9e0684e4594162f12efd/MDEC_Reference_Transcriptome/data/rna_seq/metadata). Because the read are not stranded a library type such as RF or FR will not be used.

seqType :fq - for Fastq format
max_memory :100G = max memory for trinity 100G should not be changed, per communications with the developer
CPU 36 = the number of CPUs should match the amount your computing cluster has access to, for andromeda the maximum number of cores is 36

#### a) Write Trinity Script
```
nano /data/putnamlab/flofields/ENCORE_DLAB_denovo_transcriptome/scripts/trinity.sh
```
```
#!/bin/bash
#SBATCH --job-name=20250113_trinity
#SBATCH --time=30-00:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --exclusive
#SBATCH --export=NONE
#SBATCH --mem=500GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed

#Load Trinity and picard module

module load Trinity/2.15.1-foss-2022a

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/DLAB_001_trim_R1.fastq \
--right \
/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/DLAB_001_trim_R2.fastq \
--CPU 36
```
```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/trinity.sh
Submitted batch job 355114

```
```
squeue -u ffields
```
Trinity completed successfully!!!! When Trinity completes, it creates a 'trinity_out_dir.Trinity.fasta' output file (or prefix based on the output directory you specify).

Trinity groups transcripts into clusters based on shared sequence content. Such a transcript cluster is very loosely referred to as a 'gene'. This information is encoded in the Trinity fasta accession. An example Fasta entry for one of the transcripts is formatted like so:

```
>TRINITY_DN248_c0_g1_i12 len=3995 path=[0:0-84 2:85-86 4:87-1132 5:1133-1134 6:1135-2916 7:2917-2963 10:2964-3994]
TTTTCTTCCCTAACAGACACTCTATTGTTTTGAAGGCATGAATGTATGCTTTCTTGGTCA
TCCCAGAGAGACGAATAGCCACATTCTTATCGCGCCATAATCTGTCATGTCTTCCCGCTA
AACCAACACGAAGAGCGAGGGCTCCAGAAACGTCTCTGCTTTGAACGTCATATCGGCCTA
AATCGTACTGGTACTCAATGTCGTGCTCGCAGTCCAATGTAAAGGTCCTTTTTTAGTATT
TTGTCTGGCCCCAGACCTATAAAGCAAGCTTTTTTTAAGACTGAACAGTTTTTTGGCAAA
ACATTGAGCTGCTTGCTTTACAGAAGAGGGCCAGGGATTCTTATTTTCTCTGAATCTCTA
TTGATAACACGTTCAAGTTTCCAAATTTTGTTGTTGAAATCCTGAGTAGAAAAGATCGGT
ATTAATGTTTGAGTGCTTTACTACGAAAACCCGCCTTCATGTCAGGCTATATGACAAAGC
GGCTTGATACACTGTAATGGAATAACTAAACTTCTTTCAATCAGTTCGCTGGAGATAATA
TCAGTTTATGATCAGATACTATCAGGTCTGTCAGGTTTAAGAAACTTTACTTGATAATTA
ACTATTCAACTTTGATCCAATTCTATCATGCACTTATGATGTTTATAGTTTCAGTTTAAA
TATCAACAGTGTAACCTCAAACGCTTTACACGCTAGGGCTTGAGCCATATTGTTTGAATA
ACATATGAAGTAATTAATAGTGACTATTTGTTTCGCCGTCTATAACGGTTCATGGAAGTA
ACCCACAAGTGTGATCATTCAAATTAGAGAACAGACTACACTCAGAGTCTGTAACCCGAC
TTGCTTCCACATTTGTAAGCTTGTCCAAAGAGGTTGATCATCCTTTGAGTTGAAAAGAGT
TTTTGTTACTTTGGACTTCTCAATTTGATTCTCTGACGGGAAAATCTCAACTATAGATTA
CATTTTGCATAGAATCATTTCTCTAGCAAGTCTGATCAGCTCCCTTTGATTTCACCTGAC
```
Download Trinity fasta to Desktop if needed, too large to have stored there always. Should run on your computer's terminal not Andormeda or Unity
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/trinity_out_dir.Trinity.fasta /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/DLAB_Reference_Transcriptome/data/Trinity_fasta/
```

## Assessing assembly quality
Use [Trinity toolkit utilities](https://github.com/trinityrnaseq/trinityrnaseq/wiki/Transcriptome-Contig-Nx-and-ExN50-stats) for a assembly quality assessment

#### a) Run TrinityStats.pl script for the stats output, the path of the trinity module needs to be used and then add the util folder before you can access the .pl script

This script will compute the contig Nx statistics (eg. the contig N50 value), in addition to a modification of the Nx statistic that takes into consideration transcript expression (read support) data, which we call the ExN50 statistic.

Based on the lengths of the assembled transcriptome contigs, we can compute the conventional Nx length statistic, such that at least x% of the assembled transcript nucleotides are found in contigs that are at least of Nx length. The traditional method is computing N50, such that at least half of all assembled bases are in transcript contigs of at least the N50 length value.
Should be ran while in the directory containing the fasta trinity file.
```
/opt/software/Trinity/2.15.1-foss-2022a/trinityrnaseq-v2.15.1/util/TrinityStats.pl trinity_out_dir.Trinity.fasta > trinity_assembly_stats
```
Trinity_assembly stats output 
```
################################
## Counts of transcripts, etc.
################################
Total trinity 'genes':  372737
Total trinity transcripts:      545197
Percent GC: 42.83

########################################
Stats based on ALL transcript contigs:
########################################

        Contig N10: 6105
        Contig N20: 4214
        Contig N30: 3104
        Contig N40: 2321
        Contig N50: 1714

        Median contig length: 435
        Average contig: 902.74
        Total assembled bases: 492172365
```

The N10 through N50 values are shown computed based on all assembled contigs. In this example, 10% of the assembled bases are found in transcript contigs at least 4,855 bases in length (N10 value), and the N50 value indicates that at least half the assembled bases are found in contigs that are at least 999 bases in length.

The contig N50 values can often be exaggerated due to an assembly program generating too many transcript isoforms, especially for the longer transcripts. To mitigate this effect, the script will also compute the Nx values based on using only the single longest isoform per 'gene':
```
#####################################################
## Stats based on ONLY LONGEST ISOFORM per 'GENE':
#####################################################

        Contig N10: 4855
        Contig N20: 2977
        Contig N30: 2023
        Contig N40: 1432
        Contig N50: 999

        Median contig length: 361
        Average contig: 662.98
        Total assembled bases: 247115954
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
nano /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/busco.sh
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
[ -z "$query" ] && query="${labbase}/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed/trinity_out_dir.Trinity.fasta" #set this to the query (genome/transcriptome) you are running 
[ -z "$db_to_compare" ] && db_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"
source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o busco_output -m transcriptome

echo "STOP" $(date)
```

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o busco_output -m transcriptome

echo "STOP" $(date)


```
sbatch /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/busco.sh
```
Submitted batch job 356655 on Feb 4th 2024
Submitted batch job 362309 2/23/2025


The busco_183341.log, busco_183888.log, busco_92172.log files where in the putnamlab folder. Moved them to /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs
The script output files slurm-356647.out where found in the flofields script folder. Moved them to /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/slurm_output
#### The busco_output file was found in /data/putnamlab and was moved to /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/

The entire busco dir was manually made.
```
mkdir /data/putnamlab/flofields//ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs
mkdir /data/putnamlab/flofields//ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
```
```
mv busco_183341.log /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs
mv busco_183888.log /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs
mv busco_92172.log /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs 
mv busco_184440.log /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs 
mv busco_185036.log /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs 

```
```
mv slurm-356647.out /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
mv slurm-356655.out /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
mv slurm-356654.out /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
mv slurm-356649.out /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
```

```
mv busco_output /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/busco
```
The run failed for non script errors, I checked the slurm-356655.out file and it seems that permission error. 
```
'/glfs/brick01/gv0/putnamlab/busco_downloads/file_versions.tsv'

ls -l #Checks permissions on the file/directory
```
Checked permissions and it seems Jill Ashey is the only one with persmissions so I contacted her to give my access

Job Submitted  Feb 5th 356683

### BUSCO Resuts

Transcriptomes and protein sets that are not filtered for isoforms will lead to a high proportion of duplicates. Therefore you should filter them before a BUSCO analysis".

Danielle also got a high number (78.9%) of duplicated BUSCOs in her de novo transcriptome of Apulchra, but Kevin got much less duplication (6.9%) in his Past transcriptome assembly. Danielle aligned her transcriptome with the Apul Jill and Trinity worked on. I also need to ask her if she thinks the high duplication percentage is biologically meaningful

eukaryote contaminant sequences (ftp.ncbi.nlm.nih. gov/pub/kitts/contam_in_euks.fa.gz), NCBI viral (ref_ viruses_rep_genomes) and prokaryote

<span style="color: red;">Note all Bioinfamatic work this far has been done in andormeda. I will now be using unity which is be reflected through subtle changes in the Sbatch scripts

A unity strach directory was already made so only need to make the folders to suit
directory - /scratch/workspace/ffields_uri_edu-transcriptomes

    cd /scratch/workspace/ffields_uri_edu-transcriptomes
    mkdir dlab
    cd dlab
    mkdir data
    mkdir scripts
    cd scripts

Create euk filtering script

    nano trinity_euk_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_euk_dlab
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_euk_dlab.out  # %j = job ID
    #SBATCH -e slurm-filter_euk_dlab.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Dlab/data
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_euk_dlab" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab

    echo "Unzipping contaminant database" $(date)
    gunzip -c /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_euks.fa.gz \
      > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/contam_in_euks.fa

    echo "BLASTing fasta against eukaryote contaminant sequences" $(date)

    cd /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Dlab/data

    # Step 1: Run BLAST to identify contaminants
    blastn -query trinity_out_dir.Trinity.fasta \
           -subject /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/contam_in_euks.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/contaminant_hits_euks_trinity.txt

    echo "BLAST complete, Extracting contaminant sequences from BLAST output" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/contaminant_hits_euks_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" trinity_out_dir.Trinity.fasta | sed 's/^>//' | sort \
    > /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq trinity_out_dir.Trinity.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/cleaneuk_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_euk_dlab." $(date)

```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/scripts/trinity_euk_contam.sh

Submitted batch job 38647431
```

### Had already downloaded the prokaryotic contaminant sequences by using the code below

curl -o /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa https://ftp.ncbi.nlm.nih.gov/pub/kitts/contam_in_prok.fa

### Create prokaryote filtering script

    nano trinity_prok_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_prok_dlab
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_prok_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_prok_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_prok_dlab" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab

    echo "BLASTing the fasta against prokaryote contaminant sequences" $(date)

    cd /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_euk_dlab/

    # Step 1: Run BLAST to identify contaminants
    blastn -query cleaneuk_trinity_sequences.fasta \
           -subject /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/contaminant_hits_prok_trinity.txt

    echo "BLAST complete, filtering contaminant sequences" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/contaminant_hits_prok_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneuk_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq cleaneuk_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/cleaneukandprok_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_prok_dlab." $(date)

```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/scripts/trinity_prok_contam.sh
Submitted batch job 38755096
```

### Unity has a ref virus geneome from NCBI, I will be using ref genome to filter any sequences that are encoded for viral contaminants. The path is below Create a viral filtering script

/datasets/bio/ncbi-db/2025-06-22/

```
nano trinity_filter_viral_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_viral_dlab
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_viral_mdec.out  # %j = job ID
    #SBATCH -e slurm-filter_viral_mdec.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_dlab/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_viral_mdec" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/filter_viral_dlab

echo "Run BLAST to identify any contaminants" $(date)

blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_prok_dlab/cleaneukandprok_trinity_sequences.fasta \
       -db /datasets/bio/ncbi-db/2025-06-22/ref_viruses_rep_genomes \
       -out contaminant_hits_viral_trinity.txt \
       -outfmt 6 \
       -evalue 1e-4 \
       -perc_identity 90

# Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/contaminant_hits_viral_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneukandprok_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/retained_ids.txt

             echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq cleaneukandprok_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/filter_viral_dlab/clean_euk_prok_viral_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_viral_dlab." $(date)

