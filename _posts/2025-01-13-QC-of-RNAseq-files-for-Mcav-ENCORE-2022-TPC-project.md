---
LAYOUT: Post
TITLE: QC of RNAseq Mcav files from the ENCORE 2022 TPC project 
DATE: '2025-01-13'
CATEGORIES: QC
TAGS: [Coral, Quailty Control, Montastraea cavernosa, RNAseq ]
PROJECTS: ENCORE
---

## Quality Control of RNAseq files for *Montastraea cavernosa* (*Mcav*) ENCORE 2022 TPC project  

**About**: This post details the QC of Mdec from the ENCORE 2022 Thermal performance curve (TPC) project RNAseq files. See [here](https://github.com/flofields/MDEC_Reference_Transcriptome/blob/aba1fd09f33816aff2abfebccfd25423cd0ec537/metadata/Project-Summary-ENCORE-MDEC-RNA-DNA-Extractions.md) for the project summary for the Mdec DNA and RNA extractions and [my notebook post](https://flofields.github.io/Florence_Putnam_Lab_Notebook/Madracis-decactis-denovo-transcriptome/) for the denovo transcriptome which this QC was used for.

### 1) Write and run script with raw data for checking quality with FastQC on Andromeda (untrimmed and unfiltered)

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
```
#### Submitted batch job 354836 on Jan 13 2025
#### Finished Jan 13 2025

---

#### Combined QC output into 1 file with MultiQC, a script is not needed due to fast computational time

#load module 
```
module load MultiQC/1.9-intel-2020a-Python-3.8.2
```
#Combined files 
```
multiqc /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/multiqc
```
---

#### Copied MultiQC and FastQC report to my computer : Run this in the computer's terminal not the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/multiqc /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/MCAV_Reference_Transcriptome/data/fastqc_results
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/MCAV_Reference_Transcriptome/data/fastqc_results
```
---

### The raw sequence [MultiQC Report can be found here on GitHub](https://github.com/flofields/ENCORE_Transcriptomes/blob/bd733b923bc0b62cdfba5094ff4226666d184eca/MCAV_Reference_Transcriptome/data/fastqc_results/multiqc/multiqc_report.html)
---

### Understanding a [MultiQC Report](https://nf-co.re/eager/2.5.0/docs/output#multiqc-report) and [Fastp](https://github.com/OpenGene/fastp#adapters)
### [Secondary Fastp source](https://open.bioqueue.org/home/knowledge/showKnowledge/sig/fastp)
---
These Dlab samples were pooled and had RNA concentrations of [Qbit 96.40ng/ul and Nanodrop 148.20ng/ul](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/MDEC_Reference_Transcriptome/data/rna_seq/metadata/Sample%20QC%20report%20of_30-818136646_230814121610.pdf)

 | Sample Name | % Dups| % GC | M Seqs |
 |-------------|-------|------|--------|
 | DLAB_R1_001 | 71.1% | 44%  | 206.8  |
 | DLAB_R2_001 | 66.1% | 44%  | 206.8  |
  
- Adapter content present in sequences. Adapters have not been removed yet via trimming.

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_adapter_content_plot.png)

- GC content is good.

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_per_sequence_gc_content_plot.png)

- Sequence counts shows that their is a high number of duplication which can mean over-represented sequences (warning for overrepresented sequences). This can occur when they are highly expressed genes.

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_sequence_counts_plot.png)
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_sequence_duplication_levels_plot.png)

- Quality scores are good

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_per_base_sequence_quality_plot.png)

- Low base N content

![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/cc277747f6452260f40a09305979289659a0946b/images/ENCORE/Mcav_RNAseq/fastqc_per_base_n_content_plot.png)

### 2) Trimming 
Trimming steps below were taken then another QC report was generated to decide if other trimming decisions needed to be made.

#### A new folder and script was created for the trimmed data files
```
mkdir /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/trimmed
```
```
nano /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/scripts/trim.sh
```
The trimming settings in [fastp](https://github.com/OpenGene/fastp#adapters) 
 
- --detect_adapter_for_pe 
Enables adapter sequence auto-dection. This trim is a result of the presence of Adapter content in the multiQC report

- --trim_poly_g 
Enables trimming of the polyG tails that occurs from signal degradation. This trim is a result of the sequence GC-content warning

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
#### Submitted batch job 354870 on Jan 14 2025
#### Finished Jan 14 2025
---
I then downloaded the fastp.html report into the designated folder to look at the trimming information
Check the quality of the trimmed files by confirming the number of files that were trimed and to look at the raw reads
```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw/fastp.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/Mcav_Reference_Transcriptome/data/fastp_stats
```
This file can be found [here on Github](https://github.com/flofields/ENCORE_Transcriptomes/blob/e6f1dee69ea23eff2abfb0e5147c0c09f9076765/MCAV_Reference_Transcriptome/data/fastp_stats/fastp.html)

Here are the results 

### General statistics 

| fastp version:                | 0.19.7 (https://github.com/OpenGene/fastp) |
|-------------------------------|--------------------------------------------|
| sequencing:                   | paired end (150 cycles + 150 cycles)       |
| mean length before filtering: | 150bp, 150bp                               |
| mean length after filtering:  | 147bp, 147bp                               |
| duplication rate:             | 24.067353%                                |
| Insert size peak:             | 197                                       |
| Detected read1 adapter:       | AGATCGGAAGAGCACACGTCTGAACTCCAGTCA         |
| Detected read2 adapter:       | AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT          |

### Before filtering 

| total reads: | 413.551540 M            |
|--------------|-------------------------|
| total bases: | 62.032731 G             |
| Q20 bases:   | 59.635489 G (96.135520%)|
| Q30 bases:   | 56.269454 G (90.709296%)|
| GC content:  | 44.767978%              |

### After filtering 

| total reads: | 402.949174 M            |
|--------------|-------------------------|
| total bases: | 59.516609 G             |
| Q20 bases:   | 57.706508 G (96.958663%)|
| Q30 bases:   | 54.597586 G (91.735041%)|
| GC content:  | 44.324584%              |

### Filtering results 

| reads passed filters:   | 402.949174 M (97.436265%)|
|-------------------------|--------------------------|
| reads with low quality: | 10.205084 M (2.467669%)   |
| reads with too many N:  | 37.686000 K (0.009113%)  |
| reads too short:        | 359.596000 K (0.086953%) |

These results show that filtering improved quality of reads and removed about 2% of reads due to length and qaulity. Average QC30 bases improved from 90% to 91%. I will be running another round of fastqc and multiqc to see how this changed our qc results.

- orginal files
```
#get sequence name
less  MCAV_001_trim_R1.fastq
```
```
zgrep -c "@A01587"MCAV* > seq_counts
```
- Trimmed files
###### Run comman in background so you don't have to wait to load the FastQC 
###### the & alllows for the line of code to run i the background
###### "nohup" allows for the process to keep running if you close the terminal or change the current directory


```
nohup zgrep -c "@A01587"DLAB* > trimmed_seq_counts &
```
job 45213

### 3i) Fastqc and MultiQC on trimmed sequences
#### Run fastqc on trimmed data
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

#### Submitted Batch Job 354903 Jan 15 2025
#### Finished Jan 15 2025

#### Combined QC output into 1 file with MultiQC and copied to my destop to look at the trimming information

```
#load module 
module load MultiQC/1.9-intel-2020a-Python-3.8.2

multiqc /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed
```

```
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/multiqc_trimmed /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/Mcav_Reference_Transcriptome/data/fastqc_results_trimmed
scp -r ffields@ssh3.hac.uri.edu://data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/fastqc_results_trimmed/*.html /Users/flo_f/"OneDrive - University of RHode Island"/Github/ENCORE_Transcriptomes/Mcav_Reference_Transcriptome/data/fastqc_results_trimmed
```
---



#### The raw sequence [MultiQC report can be found here in Github](https://github.com/flofields/ENCORE_Transcriptomes/blob/c7d9076c4a088dcffd61f9ed6157c5390f12462d/MCAV_Reference_Transcriptome/data/fastqc_results_trimmed/multiqc_trimmed/multiqc_report.html)

The MultiQC report results

| Sample Name |%Duplication| GC content| %PF|%Adapter| % Dups| % GC | M Seqs |
|-------------|------------|-----------|----|--------|-------|------|--------|
| Fastp       |	24.067%    |  44.32    |97.4|
| MCAV_001_trim_R1 |            |           |    |        | 71.1% | 44%  | 201.5  |
| MCAV_001_trim_R2 |            |           |    |        | 67.4% | 44%  | 201.5  |

- Fastp filtering: most reads filtered were due to low quality

- Sequence counts shows that 28.9% of reads in R1 is unique and 32.6% in R2 is unique however dulication levels/over represented sequences are high This can occur when they are highly expressed genes. It is possible to have good libraries with small peaks at high duplication levels.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_sequence_counts_plot.png)
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_sequence_duplication_levels_plot.png)

- Sequence Quality is good
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_per_base_sequence_quality_plot.png)
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_per_sequence_quality_scores_plot.png)

- Per Sequence GC Content is good 
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_per_sequence_gc_content_plot.png)

- Low base n content
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc_per_base_n_content_plot.png)

- The status check below shows the overall status for each FastQC section where green is normal, orange is slightly abnormal and red being very unsual.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/5e78d2fbeb571147fabd47f22112678f5654102c/images/ENCORE/Mcav_RNAseq/Trimmed_results/fastqc-status-check-heatmap.png)

