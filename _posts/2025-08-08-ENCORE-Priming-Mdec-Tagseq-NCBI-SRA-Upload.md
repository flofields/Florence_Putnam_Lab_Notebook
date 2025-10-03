---
LAYOUT: post
TITLE: Workflow ENCORE Priming SRA upload of Madracis decactis
CATEGORY: [ Protocol, Tagseq ]
TAG: [ Madracis decactis, Bioinformatics Tagseq, NCBI SRA, sequences ]
---

# ENCORE Priming Mdec Tagseq SRA upload TO NCBI 

This post details the NCBI Sequence Read Archive upload for the Tagseq data of ENCORE Priming experiment only focusing on Mdec molecular responses. The github repo with the data from that project can be found [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024.git). All molecular metadata can be found [here](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/tree/5dba240ac13474e911af17f6027ad065f7b6b03c/data/Molecular). This protocol is based off the Lab protocol to submit raw sequence files to [NCBI Sequence Read Archive](https://github.com/Putnam-Lab/Lab_Management/blob/master/Bioinformatics_%26_Coding/Data_Mangament/SRA-Upload_Protocol.md)

## Overview

These sequences are from the ENCORE Priming project which exams the role of environmental priming (cis = same stressor and trans = different stressor) in coral tolerance response mechanisms.


### 1. Create a BioSample first

Submitter  

	Florence Fields
	University of Rhode Island
	Biological and Environmental Sciences
	120 Flagg Rd,
	Kingston,
	Rhode Island,
	02881, USA

General Info
						
	- When should this submission be released to the public?
	  Release immediately following processing
	- Specify if you are submitting a single sample or a file containing multiple samples
	  Batch/Multiple BioSamples
      You will be asked to upload a tab-delimited text file that describes each of your samples and their attributes. Submission template files can be downloaded from the Attributes tab or the templates page.

Sample Type

	Invertebrate

Attributes

[Link to Attribute spreadsheet](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/07c4d476a08f71132955d54f1289de5805fc2cdc/NCBI_Uploads/Invertebrate.1.0%20(2).xlsx). 

It is important to note that although my sample name and title were uniquly distingushable, the sample name, title and description does not count when the sample is checking for distinguishable attributes. Since my sequences all were collected from the sample location, treated similarly and were the same species I included a colunm named sample number to show sample uniqueness to allow the spreedsheet to be uploaded and accepted.

<span style="color:red;"> BioSample Submission ID SUB15267914

The BioSample were approved under the following Accession numbers found at this [link](https://github.com/flofields/Coral_Priming_Experiments_Summer_2024/blob/07c4d476a08f71132955d54f1289de5805fc2cdc/NCBI_Uploads/BioSampleObjects.txt)

I do not have an exsisting BioProject for this data so I will be creating one

### 2. Create a BioProject

Submitter  

	Florence Fields
	ffields@uri.edu
	University of Rhode Island
	Biological and Environmental Sciences
	120 Flagg Rd,
	Kingston,
	Rhode Island,
	02881, USA

Project Type

	Project data type: Raw sequence reads
	Sample scope: Multisolate

Target

	Organism name: Madracis decactis
	Description: Adult life stage Madracis decactis samples from four colonies were subjected to an environmental priming experiment. Individuals were exposed to one of three primary treatments for two weeks: decreased pH (~7.75), heat stress (30°C), or a control temperature (27°C). Following this priming phase, all samples underwent a two-week recovery period at 28°C. Subsequently, they were exposed to a secondary stressor consisting of either continued ambient conditions (28°C) or elevated heat stress (32°C).

General Info

	When should this submission be released to the public?: Release immediately following processing
	Project title: Madracis decactis Taq-seq sequences when exposed to photosynthesis irradiance - thermal performance curves
	Public description: This study aims to understand the role of environmental priming and tolerance induced mechanisms involved in Madracis decactis' ability to tolerate heat stress. Adult life stage Madracis decactis individuals from 4 colonies were first exposed to one of three priming treatments: low pH, heat stress or control. Followed by a two week recovery period and secondary stressor exposed to either heat stress or control.
	Relevance: Environmental


BioSample
Input all biosample ids. See figure below
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/raw/master/images/SRA_images/bioproject_5BioSample_Mdec_Tagseq.png)

Publications
no publications with these sequences

The next step is review then submit.

<span style = "color:red;">BioProject submission ID SUB15549287. 
BioProject ID: PRJNA1308128 : Madracis decactis Taq-seq sequences when exposed to photosynthesis irradiance - thermal performance curves (TaxID: 123769)


### 3. Sequence Read Archive (SRA)

Submitter  

	Does not change

General Info

	Do you already have a BioProject accession number for this research?: Yes
	Existing BioProject: PRJNA1254643
	Do you already have BioSample accession numbers for these samples?: Yes
	When should this submission be released to the public?: Release immediately following processing

SRA Metadata

See here for the [SRA metadata](https://github.com/flofields/Mdec-Tagseq/blob/03b0f0e42cfb7d53802d0baec598d27618d9199b/NCBI_Uploads/SRA_metadata_acc%20(1).xlsx)

First, set up folder in Andromeda that contains symlinks to only the raw sequence files that we want to upload to NCBI.

There is already a directory called ENCORE_raw_data. I will keep using this directory but create a new dir within this dir

	mkdir ENCORE_raw_data/mkdir raw_files_rnaseq_sra_mdec_tagseq
	cd raw_files_rnaseq_sra_mdec_tagseq

I orginally wanted to symlink the files and convert them from fastq.gz to fasta but the files are pointing to each other in a loop, making it impossible for gzip to properly process them. for the purpose of uploading to NCBI copying the files then converting would be easier and best.


	cp -r /data/putnamlab/KITT/hputnam/20231101_ENCORE_Mdec_TPC/Mdec* /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra_mdec_tagseq
	gzip -d *.gz #files should change from fastq.gz to fastq

The path for downloading is /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra_mdec_tagseq .

To upload files, log on to Andromeda and enter the following:

```
cd /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra_mdec_tagseq

ftp -i

open ftp-private.ncbi.nlm.nih.gov

# enter username and password given on SRA webpage 

cd uploads/ffields_uri.edu_QKj2JZGq

mkdir ENCORE_Mdec_tagseq

cd ENCORE_Mdec_tagseq

mput *
```

The upload to SRA will proceed for each file with messages “transfer complete” when each is uploaded. Keep computer active until all uploads are finished.

Continue with the submission by selecting the preload folder on SRA.

RNAseq sequence files were submitted under SUB15276442.

Link to [BioProject](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA1254643)