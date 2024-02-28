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


#Load Trinity module

module load Trinity/2.15.1-foss-2022a
module load java -jar $EBROOTPICARD/libs/picard.jar mean

#Run Trinity

Trinity \
--seqType fq \
--max_memory 50G \
--left \
/data/putnamlab/flofields/denovo_transcriptome/MDEC_001_trim2_R1.fastq.gz \
--right \
/data/putnamlab/flofields/denovo_transcriptome/MDEC_001_trim2_R2.fastq.gz \
--CPU 36

sbatch /data/putnamlab/flofields/denovo_transcriptome/scripts/trinity.sh

Submitted batch job 304129

