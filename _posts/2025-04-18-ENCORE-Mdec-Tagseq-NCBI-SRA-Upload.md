---
LAYOUT: post
TITLE: ENCORE NVBI SRA upload of Mdec Taqseq
CATEGORY: [ Protocol ]
TAG: [ Madracis decactis, Bioinformatics Tagseq, NCBI SRA, sequences ]
---

# ENCORE Mdec Tagseq SRA upload TO NCBI 

This post details the NCBI Sequence Read Archive upload for the Tagseq data of ENCORE Pi-TPC experiment. The github repo with the data from that project can be found [here](https://github.com/hputnam/Bermuda_PICurve_TPC.git). Summary of the extraction protocol can be found [here](https://github.com/flofields/ENCORE_Transcriptomes/blob/3d9f7151e80add1718b7148b2424f8f00ada97a6/MDEC_Reference_Transcriptome/notebook_post/Project-Summary-ENCORE-MDEC-RNA-DNA-Extractions.md)  This protocol is based off the Lab protocol to submit raw sequence files to [NCBI Sequence Read Archive](https://github.com/Putnam-Lab/Lab_Management/blob/master/Bioinformatics_%26_Coding/Data_Mangament/SRA-Upload_Protocol.md)

## Overview

These sequences are from the ENCORE Photosynthesis irradiance - Thermal performance curve project which accesses how temperatur and light interact to influence the coral's photosynthesis efficiency   ps://github.com/flofields/ENCORE_Transcriptomes)


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

[Link to Attribute spreadsheet](https://github.com/flofields/Mdec-Tagseq/blob/main/NCBI_Uploads/Invertebrate.1.0.xlsx). 

It is important to note that although my sample name and title were uniquly distingushable, the sample name, titel and description does not count when the sample is checking for distinguishable attributes. Since my sequences all were collected from the sample location, treated similarly and were the same species I included a colunm named sample number to show sample uniqueness to allow the spreedsheet to be uploaded and accepted.

<span style="color:red;"> BioSample Submission ID SUB15267914

The BioSample were approved under the following numbers
SAMN48100007, SAMN48100008, SAMN48100009, SAMN48100010, SAMN48100011, SAMN48100012, SAMN48100013, SAMN48100014, SAMN48100015, SAMN48100016, SAMN48100017, SAMN48100018, SAMN48100019, SAMN4810020, SAMN48100021, SAMN48100022, SAMN48100023, SAMN48100024, SAMN48100025, SAMN48100026, SAMN48100027, SAMN48100028, SAMN48100029, SAMN48100030, SAMN48100031, SAMN48100032, SAMN48100033, SAMN48100034, SAMN48100035, SAMN48100036, SAMN48100037, SAMN48100038, SAMN48100039, SAMN48100040, SAMN48100041, SAMN48100042, SAMN48100043 


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
	Description: Adult life stage Madracis decactis samples from 6 colonies were exposed to photosynthesis irradiance - thermal performance curves at 7 different temperatures.

General Info

	When should this submission be released to the public?: Release immediately following processing
	Project title: Madracis decactis Taq-seq sequences when exposed to photosynthesis irradiance - thermal performance curves
	Public description: This study aims to understand Madracis decactis thermal performance mechanisms through exposure to photosynthesis irradiance - thermal performance curves at 7 different.
	Relevance: Environmental


BioSample
Input all biosample ids. See figure below
![](https://github.com/flofields/Florence_Putnam_Lab_Notebook/raw/master/images/SRA_images/bioproject_5BioSample_Mdec_Tagseq.png)

Publications
no publications with these sequences

The next step is review then submit.

<span style = "color:red;">BioProject submission ID SUB15274698. 
BioProject ID: PRJNA1254643 : Madracis decactis Taq-seq sequences when exposed to photosynthesis irradiance - thermal performance curves (TaxID: 123769)


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