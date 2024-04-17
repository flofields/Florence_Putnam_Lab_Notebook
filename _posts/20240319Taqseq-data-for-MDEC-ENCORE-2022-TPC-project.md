---
LAYOUT: post
TITLE: Workflow for QC of taqseq files for Madracis decactis TPC 2022 ENCORE project
CATEGORY: [ Taqseq ]
TAG: [ Madracis decactis, Taqseq ]
---

## Workflow for Encore Madracis decactis Taqseq data analysis

**About** This post contains the bioinformatic pipeline used for Quality control of Taqseq data from *Madracis decactis* samples obtained in 2022 as part of the ENCORE project in the TPC-PI experiment.

Data uploaded and analyzed on the [Andromada](https://its.uri.edu/research-computing/using-andromeda/) URI HPC location 

Location on Andromeda, the HPC server for URI:

```
cd/data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC
```
* Note: hputnam ran inital QC on raw data and output into the 20231101_ENCORE_Mdec_TPC folder

```
#Folder contents

JA23292_401745805.json             Mdec-C2_S99_L001_R1_001.fastq.gz   Mdec-E3_S93_L001_R1_001.fastq.gz
Mdec-A1_S104_L001_R1_001.fastq.gz  Mdec-C3_S100_L001_R1_001.fastq.gz  Mdec-E5_S95_L001_R1_001.fastq.gz
Mdec-A2_S107_L001_R1_001.fastq.gz  Mdec-C4_S102_L001_R1_001.fastq.gz  Mdec-E6_S97_L001_R1_001.fastq.gz
Mdec-A3_S111_L001_R1_001.fastq.gz  Mdec-C6_S105_L001_R1_001.fastq.gz  Mdec-E7_S98_L001_R1_001.fastq.gz
Mdec-A4_S113_L001_R1_001.fastq.gz  Mdec-C7_S117_L001_R1_001.fastq.gz  Mdec-F1_S101_L001_R1_001.fastq.gz
Mdec-A5_S81_L001_R1_001.fastq.gz   Mdec-D1_S108_L001_R1_001.fastq.gz  Mdec-F3_S103_L001_R1_001.fastq.gz
Mdec-A7_S82_L001_R1_001.fastq.gz   Mdec-D2_S110_L001_R1_001.fastq.gz  Mdec-F4_S106_L001_R1_001.fastq.gz
Mdec-B1_S84_L001_R1_001.fastq.gz   Mdec-D3_S114_L001_R1_001.fastq.gz  Mdec-F5_S109_L001_R1_001.fastq.gz
Mdec-B3_S86_L001_R1_001.fastq.gz   Mdec-D4_S80_L001_R1_001.fastq.gz   Mdec-F6_S112_L001_R1_001.fastq.gz
Mdec-B4_S88_L001_R1_001.fastq.gz   Mdec-D5_S83_L001_R1_001.fastq.gz   Mdec-F7_S115_L001_R1_001.fastq.gz
Mdec-B5_S90_L001_R1_001.fastq.gz   Mdec-D6_S85_L001_R1_001.fastq.gz   raw_qc
Mdec-B6_S92_L001_R1_001.fastq.gz   Mdec-D7_S87_L001_R1_001.fastq.gz   scripts
Mdec-B7_S94_L001_R1_001.fastq.gz   Mdec-E1_S89_L001_R1_001.fastq.gz   slurm-288159.out
Mdec-C1_S96_L001_R1_001.fastq.gz   Mdec-E2_S91_L001_R1_001.fastq.gz   URI.downloaded.md5
```


* Script used by hputnam for the intial QC on the raw taqseq *Mdec* data
```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH -D /data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC

module load FastQC/0.11.9-Java-11
module load MultiQC/1.9-intel-2020a-Python-3.8.2

#make qc output folder
mkdir raw_qc/

#run fastqc on raw data
fastqc *.fastq.gz -o raw_qc/

#Compile MultiQC report from FastQC files
multiqc ./raw_qc
mv multiqc_report.html raw_qc/raw_qc_multiqc_report.html
mv multiqc_data raw_qc/raw_multiqc_data

echo "Initial QC of Seq data complete." $(date)
```

Create a symlink in my ENCORE_MDEC_tagseq/data directory. A symlink link directs your folder back to the orginal raw data however changes to the raw data should not be done. This method saves memmory and space.

```
ln -s cd/data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC raw
```

## 1) Download MultiQC files to Github repo and personal folder in local computer: These lines of code should not be ran in the server

scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC/raw_qc/raw_qc_multiqc_report.html /Users/flo_f/OneDrive/Desktop/Putnam-Lab/MDEC_taqseq
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC/raw_qc/raw_qc_multiqc_report.html /Users/flo_f/"OneDrive - University of Rhode Island"/GitHub/ENCORE_MDEC/MDEC_Taqseq/Data/QC/raw

The raw sequence [MultiQC report is found here on Github](https://github.com/flofields/ENCORE_MDEC/tree/main/MDEC_Taqseq/data/QC/raw)

#### Things to note in the QC report of the raw taqseq data

* Samples with lowest sequences counts (<30%)
Mdec-A1_S104_L001_R1_001
Mdec-A3_S111_L001_R1_001
Mdec-A5_S81_L001_R1_001
Mdec-B1_S84_L001_R1_001
Mdec-B7_S94_L001_R1_001
Mdec-C1_S96_L001_R1_001
Mdec-C3_S100_L001_R1_001
Mdec-D2_S110_L001_R1_001
Mdec-E2_S91_L001_R1_001
Mdec-E3_S93_L001_R1_001

#input all samples, dups, gc and m seqs

Mean Quality Scores
Scores are high.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_per_base_sequence_quality_plot.png)

Per Base Sequence Content
The heat map show have shown a relatively equal block of color however a mixture of colors are being displayed indicating the posibility of either a poly -G tail which occurs as a result of the 2 color chemistry data from illumina novaseq or a lot of PCR duplicates. The best solution is to turn on fastP poly-G trimming with EAGER
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_per_base_sequence_content.png)

Per Sequence GC Content
High peaks seen outside the theoretical distribution which is 50% GC mark could indicate the presence of mononucleotide sequences. The trimmng solution right now is to remove the polyA tails.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_per_sequence_gc_content_plot.png)

Per N Base Content
Low N content
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_per_base_n_content_plot.png)

Sequence Duplication Levels
Indicates mono-nucleotides 
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_sequence_duplication_levels_plot.png)

Overrepresented sequences
Because i have high duplication rates and overrepresented sequences they may have had sequencing artifacts or mononucleotides
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_overrepresented_sequencesi_plot.png)

Adapter Content
Good
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc_adapter_content_plot.png)

Status Check
Showing the overall status of the multiqc report
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/8b1e1d6c61203610b5b8d51f90268ff4e8cb0b2e/images/ENCORE/Mdec_taqseq/fastqc-status-check-heatmap.png)

## 2)Trimming 

##### a) Make folders for FastQC results and scripts


	cd /data/putnamlab/flofields/denovo_transcriptome/data
	mkdir fastqc_results

	cd /data/putnamlab/flofields/denovo_transcriptome
	mkdir scripts

	cd scripts
	nano clean.sh

The trimming settings in [fastp](https://github.com/OpenGene/fastp#adapters) 
 
- --trim_poly_x (polyA) 
Enables trimming of the polyA tails that occurs from signal degradation. This trim is a result of the sequence GC-content and per base sequence fail


```
#!/bin/bash
#SBATCH -t 120:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC
#SBATCH --error=../../../flofields/ENCORE_MDEC_taqseq/data/clean/"%x_err.%j" #if your job fails, the error report will be put in this file
#SBATCH --output=../../../flofields/ENCORE_MDEC_taqseq/data/clean/"%x_out.%j" #once your job is completed, any final job report comments will be put in this file

module load fastp/0.19.7-foss-2018b
module load FastQC/0.11.8-Java-1.8
module load MultiQC/1.9-intel-2020a-Python-3.8.2

# Making an array of sequences to trim
array1=($(ls *fastq.gz))

#fastloop; trimming poly x default 10 tail (trimming polyA)
for i in ${array1[@]}; do

fastp --in1 ${i} --out1 ../../../flofields/ENCORE_MDEC_taqseq/data/clean.${i}  --trim_poly_x
fastqc ../../../flofields/ENCORE_MDEC_taqseq/data/clean.${i}

done
```
	sbatch /data/putnamlab/flofields/ENCORE_MDEC_taqseq/scripts/clean.sh
Submitted sbatch job ID 310161 on 3/27/2024

Failed - had an older module of MultiQC, change it to the updated one in the script as seen above.

Submitted sbatch job ID 310162 on 3/27/2024

---
#### Combined QC output into 1 file with MultiQC, a script is not needed due to fast computational time
```
#load module 
module load MultiQC/1.9-intel-2020a-Python-3.8.2

#Combined files 
multiqc /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data//multiqc/
```

#### Copied MultiQC and FastQC reports to my computer : Run this in the computer's terminal not the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/multiqc/multiqc_report.html /Users/flo_f/"OneDrive - University of Rhode Island"/GitHub/ENCORE_MDEC/MDEC_Taqseq/data/QC/Trim1
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/*.html /Users/flo_f/"OneDrive - University of Rhode Island"/GitHub/ENCORE_MDEC/MDEC_Taqseq/data/QC/Trim1
```

#### Needed to remove adapters. In this code I also included the parameters qualified_quality_phred 30, low_complexity_filter and complexity_threshold 50
--qualified_quality_phred 30
filters reads with a phed score of >30 allowing for good quality reads to remain.

--low_complexity_filter 
This enables the low complexity filter. This flter enables that a percentage of the bases would be different from the other

--complexity_threshold 50
This sets this filter to 50% meaning 50% of complexity is required. This ensures that 50% of bases are different from the next


```
Make folder and script for Trim 2
cd /data/putnamlab/flofields/ENCORE_MDEC_taqseq/scripts/trim2.sh
cd /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim2
```

```
#!/bin/bash
#SBATCH -t 120:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=ffields@uri.edu #your email to send notifications
#SBATCH --account=putnamlab
#SBATCH -D /data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC
#SBATCH --error=../../../flofields/ENCORE_MDEC_taqseq/data/trim3/"%x_err.%j" #if your job fails, the error report will be put in this file
#SBATCH --output=../../../flofields/ENCORE_MDEC_taqseq/data/trim3/"%x_out.%j" #once your job is completed, any final job report comments will be put in this file

module load fastp/0.19.7-foss-2018b
module load FastQC/0.11.8-Java-1.8
module load MultiQC/1.9-intel-2020a-Python-3.8.2

# Making an array of sequences to trim
array1=($(ls *fastq.gz))

#fastloop; trimming poly x default 10 tail (trimming polyA)
for i in ${array1[@]}; do

fastp --in1 ${i} --out1 ../../../flofields/ENCORE_MDEC_taqseq/data/trim3/trim3.${i}  --trim_poly_x --adapter_sequence=AGATCGGAAGAGCACACGTCTGAACTCCAGTCA --qualified_quality_phred 10 --low_complexity_filter --complexity_threshold 50
fastqc ../../../flofields/ENCORE_MDEC_taqseq/data/trim3/trim3.${i}

done

echo "Read trimming of adapters complete." $(date)

# Quality Assessment of Trimmed Reads

cd ../../../flofields/ENCORE_MDEC_tagseq/data/trim3 #The following command will be run in the /trim3 directory

multiqc ./ #Compile MultiQC report from FastQC files

echo "Cleaned MultiQC report generated." $(date)
```

	sbatch /data/putnamlab/flofields/ENCORE_MDEC_taqseq/scripts/trim3.sh
Submitted sbatch job ID 310594 on 4/06/2024

Cleaned reads
* Trim 3 - changed qualified_quality_phred 10 to 30
Submitted batch job ID 312322 

```					
#load module 
module load MultiQC/1.9-intel-2020a-Python-3.8.2
module load fastp/0.19.7-foss-2018b



#Combined files 
multiqc /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/multiqc/
fastp /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/*fastqc.zip -o /data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/fastp/

```

#### Copied MultiQC and FastQC reports to my computer : Run this in the computer's terminal not the server
```
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/multiqc/multiqc_report.html /Users/flo_f/"OneDrive - University of Rhode Island"/GitHub/ENCORE_MDEC/MDEC_Taqseq/data/QC/Trim3
scp -r ffields@ssh3.hac.uri.edu:/data/putnamlab/flofields/ENCORE_MDEC_taqseq/data/trim3/*.html /Users/flo_f/"OneDrive - University of Rhode Island"/GitHub/ENCORE_MDEC/MDEC_Taqseq/data/QC/Trim3
```

Results

General Statistics


Here are the results 

### General statistics 

| fastp version:                | 0.19.7 (https://github.com/OpenGene/fastp) |
|-------------------------------|--------------------------------------------|
| sequencing:                   |        |
| mean length before filtering: |                              |
| mean length after filtering:  |                              |
| duplication rate:             |                                 |
| Insert size peak:             |                                         |
| Detected read1 adapter:       |          |

### Before filtering 

| total reads: | 5071645            |
|--------------|-------------------------|
| total bases: | 512236145               |
| Q20 bases:   | 504067448(98.4053%)     |
| Q30 bases:   | 60.960502 G (90.881082%)|
| GC content:  |     |

### After filtering 

| total reads: | 4984633                 |
|--------------|-------------------------|
| total bases: | 501658345               |
| Q20 bases:   | 495151911(98.703%)      |
| Q30 bases:   | 480594040(95.8011%)     |
| GC content:  |            |

### Filtering results 

| reads passed filters:   | 4984633|
|-------------------------|--------------------------|
| reads with low quality: | 45416                    |
| reads with too many N:  | 47  |
| reads too short:        | 25396 |
| reads failed due to low complexity: | 16153 |
| reads with adapter trimmed: | 7921 |
|bases trimmed due to adapters: | 181527 |

Mean Quality Scores
Scores are high.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_per_base_sequence_quality_plot.png)

Per Base Sequence Content
This heat map and line plot shows the base positions of each DNA, in tagseq data reads tend to run parallel with each other but should not be hugely imbalanced from each other. Warnings are common
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_per_base_sequence_content_2.png)
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_per_base_sequence_content.png)

Per Sequence GC Content
High peaks seen outside the theoretical distribution which is 50% GC mark can indicate the presence of mononucleotide sequences however, the polyA tails were removed. Another possible reason could be species contamination, this is called broader which can occur when the sample contains DNA from multiple organisms. Multiple peaks are seen corresponding to the different genomes
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/ENCORE/Mdec_tagseq_trim3/fastqc_per_sequence_gc_content_plot.png)

Per N Base Content
Low N content
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_per_base_n_content_plot.png)

Sequence Duplication Levels
Can indicate enrichment bias. Over-sequencing highly expressed transcripts can lead to a large set of duplicates and FastQC tend to overestimate duplicate reads due to biases.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_sequence_duplication_levels_plot.png)

Overrepresented sequences
This could be a result of having high duplication rates and overrepresented sequences they may have had sequencing artifacts or mononucleotides. The most likely cause would be highly expressed genes.In this case it is not an issue because less than 2% of overrepresented genes.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_overrepresented_sequencesi_plot.png)

Sequence Length Distribution
Shows that the sequences are not the same and is due to the trimming parameters used.
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc_sequence_length_distribution_plot.png)

Status Check
Showing the overall status of the multiqc report
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/7a8e3f351217323956c2514ed5e0a962cc5d4e6a/images/ENCORE/Mdec_tagseq_trim3/fastqc-status-check-heatmap.png)
