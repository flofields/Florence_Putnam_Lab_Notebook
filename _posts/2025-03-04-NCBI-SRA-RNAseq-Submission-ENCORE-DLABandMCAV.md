---
LAYOUT: post
TITLE: Submitting raw RNAseq sequences from the ENCORE project to NCBI SRA
CATEGORY: [ Protocol ]
TAG: [ Diploria labyrinthiformis, Montastrea cavernosa, RNAseq, NCBI SRA, sequences ]
---

## NCBI Sequence Read Archive uploads

This post is a continuation of the NCBI Sequence Read Archive for F. Field's ENCORE Denvo trancriptomes project. This protocol is based off the Lab protocol to submit raw sequence files to [NCBI Sequence Read Archive](https://github.com/Putnam-Lab/Lab_Management/blob/master/Bioinformatics_%26_Coding/Data_Mangament/SRA-Upload_Protocol.md)

### Overview

These sequences are from the ENCORE Thermal performance curve project used to assemble denovo transcriptomes. The Denovo Transcriptome project can be forund [HERE](https://github.com/flofields/ENCORE_Transcriptomes)

Tropical corals (Diploria labyrinthiformis, Montastrea cavernosa and Madracis decactis) from Bermuda were exposed to Photosynthesis-Irradiance curves for thermal performance curves (PI-TPC) at temperatures 16-40℃. 

For the purpose of the reference transcriptomes RNA was pooled using the Zymo Clean and Concentrate Kit from multiple samples exposed to temperatures 16℃, 22℃, 29℃ and 36℃ during the TPC to capture genetic diversity and treatment diversity for the reference transcriptomes. Pooled RNA samples were sent to Azenta where they underwent a standard mRNA-seq paired end with ployA selection (non-directional). Lbraries were sequenced on Illumina NovaSeq 6000 and generated 2x150bp.

#### 1. BioProject
I will be using the same [BioProject](https://www.ncbi.nlm.nih.gov/bioproject/?linkname=biosample_bioproject&from_uid=47004859) to upload the raw reads of Diploria labyrinthiformis and Montastrea cavernosa. 
Accession: 	PRJNA1228646

#### 2. BioSample
Using the Invertebrate attribute table b/c I'm uploading adult coral samples. I deleted some of the columns that I wasn't using. 

Trying to submit Invertebrate attribute file, but I keep getting errors. This is an example of the rows in my attribute table:

|*sample_name|sample_title|bioproject_accession|*organism|isolate|breed|host|isolation_source|*collection_date|*geo_loc_name|*tissue|age|altitude|biomaterial_provider|collected_by|depth|dev_stage|env_broad_scale|host_tissue_sampled|identified_by|lat_lon|temp|description|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
Dlab_R1_R2|FF2|PRJNA1228646|Diploria labyrinthiformis|Coral host|Not applicable|Not applicable|Not applicable|2022-08-05|Bermuda|Whole organism|Adult|Not applicable|Not applicable|Bermuda Institue of Ocean Sciences|Not applicable|Adult|coral reef [ENVO:00000150]|Whole host organism|Jean-Baptiste Lamarck|32.371857°N 64.742464°W|samples were pooled from temperatures 16℃, 22℃,29℃ and 36℃|Adult life stage sample of Diploria labyrinthiformis collected from Bermuda, at on of the patch reefs in Bailey's Flats. Individuals were transported to the Bermuda Institue of Ocean Sciences and underwent exposure Photosynthesis-Irradiance at 7 temperatures. Indivuals were clipped to <1cm2 and placed into tubes containing 1ml of RNA/DNA Shield and stored at -80°C until processing. RNA was extracted and pooled from temperatures 16℃, 22℃,29℃ and 36℃ and sent to Azenta for library prep and sequencing|
Mcav_R1_R2|FF3|PRJNA1228646|Montastrea cavernosa| Coral host|Not applicable|Not applicable|Not applicable|2022-08-05|Bermuda|Whole organism|Adult|Not applicable|Not applicable|Bermuda Institue of Ocean Sciences|Not applicable|Adult|coral reef [ENVO:00000150]|Whole host organism|Carl Linnaeus|32.371857°N 64.742464°W|samples were pooled from temperatures 16℃, 22℃,29℃ and 36℃| Adult life stage sample of Montastrea cavernosa collected from Bermuda, at on of the patch reefs in Bailey's Flats. Individuals were transported to the Bermuda Institue of Ocean Sciences and underwent exposure Photosynthesis-Irradiance at 7 temperatures. Indivuals were clipped to <1cm2 and placed into tubes containing 1ml of RNA/DNA Shield and stored at -80°C until processing. RNA was extracted and pooled from temperatures 16℃, 22℃,29℃ and 36℃ and sent to Azenta for library prep and sequencing|

Manatory fields were highlighted in green and were identified as sample_name, organism, collection_date, geo_loc_name, tissue
Fields highlighed in blue meant i have to fill at least one of those fields. I filled out the isolate field and indicated not applicable for the fields breed, host and isolation_source.
Non-manatory fields filled were sample_title, bioproject_accession,age,collected_by, dev_stage,env_broad_scale,	host_tissue_sampled,identified_by,lat_lon,temp and description

My attribute file can be found [here](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/NCBI_Uploads/Invertebrate.1.0.xlsx)

submission number SUB15111384

The BioSample were approved under the following numbers  SAMN47213160, SAMN47213161
|Accession | Sample Name  | SPUID | Organism | Tax ID | Breed | Isolate| BioProject | Link|
|---|---|---|---|---|---|---|---|---|---|
|SAMN47213160| Dlab_R1_R2| Dlab_R1_R2 | Diploria labyrinthiformis |242715|Not applicable | Coral host | PRJNA911752 |https://dataview.ncbi.nlm.nih.gov/object/SAMN47213160 |
|SAMN47213161| Mcav_R1_R2| Mcav_R1_R2| Montastrea cavernosa |63558|Not applicable | Coral host | PRJNA911752 |https://dataview.ncbi.nlm.nih.gov/object/SAMN47213161 |

#### 3. Sequence Read Archive (SRA)

See here for the [SRA metadata](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/NCBI_Uploads/SRA_metadata2.xlsx)

First, set up folder in Andromeda that contains symlinks to only the raw sequence files that we want to upload to NCBI.

There is already a directory called ENCORE_raw_data. I will keep using this directory but create a new dir within this dir
```
mkdir ENCORE_raw_data/mkdir raw_files_rnaseq_sra_dlab_mcav
cd mkdir raw_files_rnaseq_sra_dlab_mcav

#Symlink to raw data
ln -s /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw/DLAB_R1_001.fastq .
ln -s /data/putnamlab/flofields/ENCORE_Dlab_denovo_transcriptome/data/raw/DLAB_R2_001.fastq .
ln -s /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw/MCAV_R1_001.fastq .
ln -s /data/putnamlab/flofields/ENCORE_Mcav_denovo_transcriptome/data/raw/MCAV_R2_001.fastq .
```

The path for downloading is /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra .

To upload files, log on to Andromeda and enter the following:

```
cd /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra_dlab_mcav/

ftp -i

open ftp-private.ncbi.nlm.nih.gov

# enter username and password given on SRA webpage 

cd uploads/ffields_uri.edu_QKj2JZGq

mkdir ENCORE_transcriptomes_upload_rnaseq_2

cd ENCORE_transcriptomes_upload_rnaseq_2

mput *
```

The upload to SRA will proceed for each file with messages “transfer complete” when each is uploaded. Keep computer active until all uploads are finished.

Continue with the submission by selecting the preload folder on SRA.

RNAseq sequence files were submitted under SUB15153279.

Link to [BioProject](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA1228646)