---
LAYOUT: post
TITLE: Workflow for Montastraea cavernosa de novo transcriptome
CATEGORY: [ de novo transcriptome, RNA seq ]
TAG: [ Montastraea cavernosa, de novo transcriptome ]
---
## Designing a workflow to create a de novo transcriptome for *Montastraea cavernosa*

**About**: This post details the steps taken to create a de novo transcriptome for *Montastraea cavernosa* using samples obtained in August 2022 as part of the ENCORE project in the TPC-PI experiment. 

### *MCAV* transcriptome data files on URI andromeda:

Location on Andromeda, the HPC server for URI:

```
cd/data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes
```

```
MCAV_R1_001.fastq.gz
MCAV_R1_001.fastq.gz.md5
MCAV_R2_001.fastq.gz
MCAV_R2_001.fastq.gz.md5
```

All files were downloaded to andromeda URI HPC location

Created directory for fastq files in home folder names flofields

```
mkdir ENCORE_Mcav_denovo_transcriptome
cd ENCORE_Mcav_denovo_transcriptome
mkdir data
cd data
mkdir raw
```

Copied all data files to new location on Andromeda

```
cp -r /data/putnamlab/KITT/hputnam/20230825_Bermuda_Reference_Transcriptomes/MCAV* /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw

MCAV_R1_001.fastq.gz
MCAV_R1_001.fastq.gz.md5
MCAV_R2_001.fastq.gz
MCAV_R2_001.fastq.gz.md5
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
rm MCAV_R1_001.fastq.gz.md5
rm MCAV_R2_001.fastq.gz.md5
```

##### d) Checked latest module
```
module -avail #shows the lastest module release to be used
```

##### e) Write script for checking quality with FastQC, submit as job on Andromeda and make folder for scripts and the fastqc results

```
mkdir scripts
mkdir fastqc_results
```
```
nano /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/fastqc.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw/MCAV*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/fastqc.sh

Submitted batch job 354836
Date of Submission 20250113
```

---
## 2) Combined QC output into 1 file with MultiQC, do not need a script due to fast computational time
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/multiqc
```
---
## 3) Copy MultiQC and FastQC files to local computer: These lines of code should not be ran in the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/multiqc /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/MCAV_Reference_Transcriptome/data/fastqc_results
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/MCAV_Reference_Transcriptome/data/fastqc_results
```
---
### [Output MultiQC Report](https://github.com/flofields/ENCORE_Transcriptomes/blob/bd733b923bc0b62cdfba5094ff4226666d184eca/MCAV_Reference_Transcriptome/data/fastqc_results/multiqc/multiqc_report.html)
---
### Understanding a [MultiQC Report](https://nf-co.re/eager/2.5.0/docs/output#multiqc-report) and [Fastp](https://github.com/OpenGene/fastp#adapters)
### [Secondary Fastp source](https://open.bioqueue.org/home/knowledge/showKnowledge/sig/fastp)

## 4) Trim and clean reads

##### a) Make trimmed reads folder in data file

```
mkdir /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed
```


### For a more detailed look at the QC steps see [HERE](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/_posts/2025-01-13-QC-of-RNAseq-files-for-Mcav-ENCORE-2022-TPC-project.md)

##### b) Write script for Trimming and run on Andromeda

Run fastq on files to check the fastp.html report to look at the trimming information

Trims adapter
Trims poly G tail if present

```
nano /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/trim.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw

module load fastp/0.19.7-foss-2018b

fastp --in1 MCAV_R1_001.fastq --in2 MCAV_R2_001.fastq --detect_adapter_for_pe --trim_poly_g --out1 /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/MCAV_001_trim_R1.fastq --out2 /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/MCAV_001_trim_R2.fastq
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/trim.sh
```
#### Submitted job 354870
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
eg. less MCAV_001_trim_R2.fastq
```
```
zgrep -c "@A01587"MCAV* > seq_counts

MDEC_R1_001.fastq:223590727
MDEC_R2_001.fastq:223590727
```
###### look at trimmed reads
###### the & alllows for the line of code to run i the background
###### "nohup" allows for the process to keep running if you close the terminal or change the current directory
```
nohup zgrep -c "@A01587"MCAV* > trimmed_seq_counts &
45213 job number
```

## 6)Run Fastq on trimmed reads
```
mkdir /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed
```
```
nano /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/fastqc_trimmed.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

module load FastQC/0.11.9-Java-11

for file in /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/MCAV*
do
fastqc $file --outdir /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/
done
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/fastqc_trimmed.sh
```
Submitted Batch Job 354903

---
## 7) Run MultiQC on trimmed data, Combined QC output into 1 file with MultiQC

```
#load module 
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed
```
---
## 8) Copy multiqc and fastqc to computer, use terminal window from desktop not in server
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/Mcav_Reference_Transcriptome/data/fastqc_results_trimmed
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/Mcav_Reference_Transcriptome/data/fastqc_results_trimmed
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
nano /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/trinity.sh
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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed

#Load Trinity module

module load Trinity/2.15.1-foss-2022a

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/Mcav_001_trim_R1.fastq.gz \
--right \
/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/Mcav_001_trim_R2.fastq.gz \
--CPU 36
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/trinity.sh
Submitted batch job 355073

```
```
squeue -u ffields
```
Trinity was unsucsessful. The issue was the inclusion of .gz in the R1 and R2 files and they could not locate the file. the .gz was removed and the Mcav_001....was changed to MCAV_001 ... script again 

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
#SBATCH -D /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed
#SBATCH --error="script_error" #if your job fails, the error report will be put in this file
#SBATCH --output="output_script" #once your job is completed, any final job report comments will be put in this file

cd /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed

#Load Trinity module

module load Trinity/2.15.1-foss-2022a

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/MCAV_001_trim_R1.fastq.gz \
--right \
/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/MCAV_001_trim_R2.fastq.gz \
--CPU 36
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/trinity.sh
Submitted batch job 356656
```
Trinity completed successfully!!!! When Trinity completes, it creates a 'trinity_out_dir.Trinity.fasta' output file (or prefix based on the output directory you specify).

Trinity groups transcripts into clusters based on shared sequence content. Such a transcript cluster is very loosely referred to as a 'gene'. This information is encoded in the Trinity fasta accession. An example Fasta entry for one of the transcripts is formatted like so:

```
>TRINITY_DN100714_c0_g1_i2 len=776 path=[0:0-519 2:520-775]
ATAATATTCCCATATTTTGAGATATAACCACCGACCAGCCGCCTCCTTCATTTATGAAGT
TGCTAAGTTCTGGAAGCTGTCAGAGAAACAAAACAGAAATTTATTATCAAAAGTAAACTT
TGACATTTTATATTTTTTGTTCGTCTTCCCAGGATGCCAGGCGCCTTTCTCACAACGCCC
TGAAATTCTGCCAGTAGCAGGAACTGGGAAAAACTTTAACTGAACAAATACGTATATCAG
AAGAGAGGCATAAACAACCTGGTCTAAAACATGCGCACCTTGGAGGAATCTTTAAGTTAA
AATTTTTAGTTGACCGTTTTATTTTACGCCACATCACAGACAACTAGAATTCGTTTAATT
AGCTTGGTTTACAGCAGATTTCCCGCGCTTAGGACCAGTTTGTTTGGCGACATGGTTTTC
GTGACTTCGAAATTTGAACCGGTAAAATCTAGAAGACTTTTGTAAGAAACCGTTATGAAA
CGAGCAGTGGCAAATCAAATTACGAGCCGATAAACTAGATGGATAACAAATATTAAATTC
TTTGTGTGCAAGTGGCGTACAAAGCGTAACTTTTCAGTAACGACCCAGTAACTGTCTTTT
GGTTTGAAACTGGAGGATTTCGGAAATTTGAGTCGTTATTAAATTGACCAGCAGTTTTGG
CGGGAAATACTGCGGGAACTGCTCGTACGCAATGTTGTGGAGTGCTTGGCACCTTTATCA
GTTTACATGTTTTTTCGCACTTTGCTCTGTACTAGTTTATCAGGTTATGTTTTCCC
```
Download Trinity fasta to Desktop if needed, too large to have stored there always. Should run on your computer's terminal not Andormeda or Unity
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/trinity_out_dir.Trinity.fasta /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/MCAV_Reference_Transcriptome/data/Trinity_fasta/
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
Total trinity 'genes':  437746
Total trinity transcripts:      634134
Percent GC: 44.33

########################################
Stats based on ALL transcript contigs:
########################################

        Contig N10: 5332
        Contig N20: 3594
        Contig N30: 2620
        Contig N40: 1966
        Contig N50: 1456

        Median contig length: 411
        Average contig: 813.01
        Total assembled bases: 515556578
```
The N10 through N50 values are shown computed based on all assembled contigs. In this example, 10% of the assembled bases are found in transcript contigs at least 5,332 bases in length (N10 value), and the N50 value indicates that at least half the assembled bases are found in contigs that are at least 1456 bases in length.

The contig N50 values can often be exaggerated due to an assembly program generating too many transcript isoforms, especially for the longer transcripts. To mitigate this effect, the script will also compute the Nx values based on using only the single longest isoform per 'gene':

```
#####################################################
## Stats based on ONLY LONGEST ISOFORM per 'GENE':
#####################################################

        Contig N10: 4346
        Contig N20: 2718
        Contig N30: 1896
        Contig N40: 1347
        Contig N50: 934

        Median contig length: 351
        Average contig: 634.12
        Total assembled bases: 277584354
```
You can see that the Nx values based on the single longest isoform per gene are lower than the Nx stats based on all assembled contigs, as expected, and even though the Nx statistic is really not a reliable indicator of the quality of a transcriptome assembly, the Nx value based on using the longest isoform per gene is perhaps better for reasons described above.

## 10) Run Busco (Benchmarking Universal Single-Copy Orthologs)

- Commands and overview for running BUSCO here: https://busco.ezlab.org/busco_userguide.html

- uses highly conserved single-copy orthologs; evolutionary informed expectations of gene content.

- appears that you can focus a BUSCO analysis to orthologs related to your target taxa.

- below shows a BUSCO analysis comparing the crayfish targetted for the novo transcriptome assembly to 44 other arthropod species assemblies and a single vertebrate assembly:

[Theissinger et al. 2016](https://www.sciencedirect.com/science/article/abs/pii/S1874778716300137) 
Citation: Theissinger, K., Falckenhayn, C., Blande, D., Toljamo, A., Gutekunst, J., Makkonen, J., ... & Kokko, H. (2016). De Novo assembly and annotation of the freshwater crayfish Astacus astacus transcriptome. Marine Genomics, 28, 7-10.

#### a) Make run-busco-transcriptome.sh script and busco folders

mkdir /data/putnamlab/flofields//ENCORE_Dlab_denovo_transcriptome/data/busco/busco_logs
mkdir /data/putnamlab/flofields//ENCORE_Dlab_denovo_transcriptome/data/busco/slurm-output
```
nano /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/busco.sh
```
```
#!/bin/bash
#SBATCH --job-name="busco"
#SBATCH --time="100:00:00"
#SBATCH --nodes 1 --ntasks-per-node=20
#SBATCH --mem=250G
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
##SBATCH --output="busco-%u-%x-%j"
##SBATCH --account=putnamlab
##SBATCH --export=NONE


echo "START" $(date)

labbase=/data/putnamlab
busco_shared="${labbase}/shared/busco"
[ -z "$query" ] && query="${labbase}/flofields/ENCORE_Mcav_denovo_transcriptome/data/trimmed/trinity_out_dir.Trinity.fasta" #set this to the query (genome/transcriptome) you are running 
[ -z "$db_to_compare" ] && db_to_compare="${busco_shared}/downloads/lineages/metazoa_odb10"

source "${busco_shared}/scripts/busco_init.sh"  # sets up the modules required for this in the right order

# This will generate output under your $HOME/busco_output
cd "${labbase}/${flofields}"
busco --config "$EBROOTBUSCO/config/config.ini"  -f -c 20 --long -i "${query}" -l metazoa_odb10 -o busco_output -m transcriptome

echo "STOP" $(date)
```
```
sbatch /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/scripts/busco.sh
```
Submitted batch job 360869

mv busco_output /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data


<span style="color: red;">Note all Bioinfamatic work this far has been done in andormeda. I will now be using unity which is be reflected through subtle changes in the Sbatch scripts

A unity strach directory was already made so only need to make the folders to suit
directory - /scratch/workspace/ffields_uri_edu-transcriptomes

    cd /scratch/workspace/ffields_uri_edu-transcriptomes
    mkdir mcav
    cd mcav
    mkdir data
    mkdir scripts
    cd scripts

#### Create euk filtering script

    nano trinity_euk_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_euk_mcav
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_euk_mcav.out  # %j = job ID
    #SBATCH -e slurm-filter_euk_mcav.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Mcav/data
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_euk_mcav" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav

    echo "Unzipping contaminant database" $(date)
    gunzip -c /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_euks.fa.gz \
      > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/contam_in_euks.fa

    echo "BLASTing fasta against eukaryote contaminant sequences" $(date)

    cd /project/pi_hputnam_uri_edu/ffields/Transcriptomes/Mcav/data

    # Step 1: Run BLAST to identify contaminants
    blastn -query trinity_out_dir.Trinity.fasta \
           -subject /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/contam_in_euks.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/contaminant_hits_euks_trinity.txt

    echo "BLAST complete, Extracting contaminant sequences from BLAST output" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/contaminant_hits_euks_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" trinity_out_dir.Trinity.fasta | sed 's/^>//' | sort \ 
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq trinity_out_dir.Trinity.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/cleaneuk_trinity_sequences.fasta

    echo "Processing complete. All outputs saved in filter_euk_mcav." $(date)

```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/scripts/trinity_euk_contam.sh

Submitted batch job 38656841
```
### Had already downloaded the prokaryotic contaminant sequences by using the code below

curl -o /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa https://ftp.ncbi.nlm.nih.gov/pub/kitts/contam_in_prok.fa

### Create prokaryote filtering script

nano trinity_prok_contam.sh
```
    #!/bin/bash
    #SBATCH --job-name=filter_prok_mcav
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_prok_mcav.out  # %j = job ID
    #SBATCH -e slurm-filter_prok_mcav.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/
    #SBATCH --constraint=avx512


    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_prok_mcav" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav

    echo "BLASTing the fasta against prokaryote contaminant sequences" $(date)

    cd /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_euk_mcav/

    # Step 1: Run BLAST to identify contaminants
    blastn -query cleaneuk_trinity_sequences.fasta \
           -subject /project/pi_hputnam_uri_edu/ffields/dbs/contam_in_prok.fa \
           -task megablast -outfmt 6 -evalue 4 -perc_identity 90 \
           -num_threads 15 \
           -out /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/contaminant_hits_prok_trinity.txt

    echo "BLAST complete, filtering contaminant sequences" $(date)

    # Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/contaminant_hits_prok_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneuk_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/retained_ids.txt

    echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq cleaneuk_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_prok_mcav/cleaneukandprok_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_prok_mcav." $(date)
```
sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/scripts/trinity_prok_contam.sh
Submitted batch job 38760979

### Unity has a ref virus geneome from NCBI, I will be using ref genome to filter any sequences that are encoded for viral contaminants. The path is below Create a viral filtering script

/datasets/bio/ncbi-db/2025-06-22/


    nano trinity_filter_viral_contam.sh

    #!/bin/bash
    #SBATCH --job-name=filter_viral_mcav
    #SBATCH --nodes=1 --cpus-per-task=15
    #SBATCH --mem=200G  # Requested Memory
    #SBATCH --time=24:00:00
    #SBATCH -o slurm-filter_viral_mcav.out  # %j = job ID
    #SBATCH -e slurm-filter_viral_mcav.err  # %j = job ID
    #SBATCH --mail-type=BEGIN,END,FAIL
    #SBATCH --mail-user=ffields@uri.edu
    #SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/
    #SBATCH --constraint=avx512

    module load uri/main BLAST+/2.15.0-gompi-2023a
    module load uri/main seqtk/1.4-GCC-12.3.0

    echo "Creating output directory: filter_viral_mcav" $(date)
    mkdir -p /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav

    echo "Run BLAST to identify any contaminants" $(date)

    blastn -query /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/cleaneukandprok_trinity_sequences.fasta \
       -db /datasets/bio/ncbi-db/2025-06-22/ref_viruses_rep_genomes \
       -out contaminant_hits_viral_trinity.txt \
       -outfmt 6 \
       -evalue 1e-4 \
       -perc_identity 90

# Step 2: Extract contaminant IDs from BLAST output
    awk '{print $1}' /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/contaminant_hits_viral_trinity.txt | sort | uniq > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/contaminant_ids.txt

    # Step 3: Create list of sequences to KEEP (non-contaminants)
    grep "^>" cleaneukandprok_trinity_sequences.fasta | sed 's/^>//' | sort \
    > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/all_sequence_ids.txt
    comm -23 /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/all_sequence_ids.txt \
             /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/contaminant_ids.txt \
             > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/retained_ids.txt

             echo "Filtering non-contaminant sequences with seqtk" $(date)

    # Step 4: Remove contaminants using seqtk
    seqtk subseq /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/data/filter_prok_mdec/cleaneukandprok_trinity_sequences.fasta \
            /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/retained_ids.txt \
            > /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/filter_viral_mcav/clean_euk_prok_viral_trinity_sequences.fasta

    echo "Done. All outputs saved in filter_viral_mdec." $(date)

sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/scripts/trinity_filter_viral_contam.sh
Submitted batch job 39208925

### Filitering sym sequences from the fasta file

Download symbiont files from [marine genomics](https://marinegenomics.oist.jp/symb/gallery) in designated folder and unzipped them

    curl -L -o symbB.v1.0.genome.fa.gz "https://marinegenomics.oist.jp/symb/download/symbB.v1.0.genome.fa.gz"
    curl -L -o symC_scaffold_40.fasta.gz "https://marinegenomics.oist.jp/symb/download/symC_scaffold_40.fasta.gz"