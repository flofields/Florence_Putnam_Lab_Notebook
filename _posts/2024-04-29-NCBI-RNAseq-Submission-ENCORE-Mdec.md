---
LAYOUT: post
TITLE: Workflow for Submitting raw RNAseq sequences from the ENCORE project to NCBI SRA
CATEGORY: [ Protocol ]
TAG: [ Madracis decactis, Taqseq, RNAseq, NCBI SRA, sequences ]
---

## Adding RNAseq and Taqseq sequence files to NCBI Sequence Read Archive 

**About** This post follows the Lab protocol to submit raw sequence files to [NCBI Sequence Read Archive](https://github.com/Putnam-Lab/Lab_Management/blob/master/Bioinformatics_%26_Coding/Data_Mangament/SRA-Upload_Protocol.md)

# NCBI Archive Structure

[BioProject](https://www.ncbi.nlm.nih.gov/bioproject/): "A BioProject is a collection of biological data related to a single initiative, originating from a single organization or from a consortium. A BioProject record provides users a single place to find links to the diverse data types generated for that project." All data and samples from this initiative reside under the one BioProject.

[BioSample](https://www.ncbi.nlm.nih.gov/biosample): "The BioSample database contains descriptions of biological source materials used in experimental assays." In our work, this is one individual. Multiple different sequence types can come from one individual (RNA, DNA, microbial, etc.) however they all come from a specific individual with specific attributes.

[SRA - Sequence Read Archive](https://www.ncbi.nlm.nih.gov/sra/) "Sequence Read Archive (SRA) data, available through multiple cloud providers and NCBI servers, is the largest publicly available repository of high throughput sequencing data. The archive accepts data from all branches of life as well as metagenomic and environmental surveys. SRA stores raw sequencing data and alignment information to enhance reproducibility and facilitate new discoveries through data analysis." A single SRA submission (or in their language: an EXPERIMENT) is a unique sequencing result for a specific sample. In many of your projects you may have different sequencing results from one sample (RNA, DNA, microbial, etc.).

![1](https://raw.githubusercontent.com/Putnam-Lab/Lab_Management/master/Bioinformatics_%26_Coding/images/NCBI-Archive.jpg)

# Submission Workflow
It seems intuitive to create a BioProject before making the BioSamples, but if you do that the BioProject portal will take you to make BioSamples, and it won't let you upload a batch file when it takes you that way. That's why it is **much easier** to make the BioSamples before the project. You can add the BioProject accession number to your samples after it is created. It is definitely better to have created both the BioProject and all of your BioSamples for what you are trying to upload before you start the SRA submission process. Like most things, it gets more confusing the more things you try to do at once.

![2](https://raw.githubusercontent.com/Putnam-Lab/Lab_Management/master/Bioinformatics_%26_Coding/images/SRA_Workflow.jpg)

#### Information to gather before processing
- Species name and if there is any potential complications with that
- Who collected your samples (the organism) and the exact date
- The collection location (named location and latitude, longitude)
- How much of your organism was collected (whole body or certain tissues/organs)
- The batch that your samples were processed in (extraction, library, etc)
- All other potential metadata and characteristics specific to your samples: treatments, temperatures, biological replicates, developmental stages, genotypes, tank number, etc.
- The type of sequencing done for your upload
- The source of your nucleotide information (ie. RNA, DNA, metagenomic)
- How sequences were selected for library preparation (ie. restriction digest, PCR, PolyA enrichment, other enrichment or hybridizations, etc)
- How and by whom libraries were prepped, and what kit or method was used
- Sequencing platform (most likely Illumina)
- Which sequencing machine was used (ie. HiSeq #, MiSeq, NovaSeq, etc.)
- Whether your sequences are single or paired end
- Sequence file type

Once you have gathered those, the next thing to do is **login to the [submission portal for NCBI](https://submit.ncbi.nlm.nih.gov/)**.

## 1 BioSample Submission

- Once logged in, click on the tab that says **My Submissions**.
- In the Start a New Submission box, click on **BioSample**. This will take you to all your BioSamples.  

![3](https://raw.githubusercontent.com/Putnam-Lab/Lab_Management/master/Bioinformatics_%26_Coding/images/new_sub.png)

- Right **next** to the blue box that says **New Submission** 
- The first step is to fill out the submitter information, next general info which ask when the submission should be releases to the public and to specfic submission of a single or file containing multiple samples
![4](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/4773cd336c38a557052fe3a5eff504112800ba1b/images/SRA_images/general_information.jpg?raw=true)
- Click **Release immediately following processing and batch/Multiple Biosamples**.
![5](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/a6d30d3b530a898dc47b48450f6929a26f3701c7/images/SRA_images/step_2.png?raw=true)
- The thrid step is to fill out the sample type portion. Choose the sample type that your samples fit under. **Most often it is invertebrate. However if you know these samples include/are for holobiont or microbiome or symbiont sequences you want to choose the MIMS Environmental/Metagenome host associated excel sheet**  
![6](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/a6d30d3b530a898dc47b48450f6929a26f3701c7/images/SRA_images/step_3.png?raw=true)

Note: I chose MIMS Enviromental/Metagenome but then emailed NCBI to have them change it to invertebrate
- Step four, fill out the attributes page. choose upload a file using Excel or text format then download Excel option for the template

All the examples in this workflow are going to be with the Invertebrate sample type. If you're using the metagenome type most of these still apply, and other headings should be intuitive after looking through this.
Components of the downloaded spreadsheet to fill out (highlighted in green means mandatory):

Sample Name : unique name to identify your sample, can be abbreviated using consistent abbreviations from your data
Sample Title : not required, but you should write out a more descriptive name for your sample from your sample_name because these will be the link title when these samples are on NCBI
BioProject accession : you likely don't have this yet, so leave it blank if there isn't a project yet and it can be added in later
Organism : full scientific name of species. If you are using the metagenome file, say metagenome here.
Collected date : day the tissue/organism was taken
env_broad_scale : Add terms that identify the major environment type(s) where your sample was collected
env_local_scale : Add terms that identify environmental entities having causal influences upon the entity at time of sampling
env_medium : Add terms that identify the material displaced by the entity at time of sampling. Recommend subclasses of environmental material.
Geo-loc name : Word description of location; Country : State/Province : City/locality
lat_lon : The geographical coordinates of the location where the sample was collected. Specify as degrees latitude and longitude

Here is an example of my attribute table
Link to attribute table found [here](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/NCBI_Uploads/MIMS.me.host-associated.6.xlsx)

|sample_name|	sample_title|	bioproject_accession|	*organism|	*collection_date|	*env_broad_scale|*env_local_scale|*env_medium|*geo_loc_name	|*host	|*lat_lon|altitude|ances_data|biol_stat|chem_administration|collection_method|depth|elev|genetic_mod|gravidity|host_age	|host_blood_press_diast|host_blood_press_syst|host_body_habitat|host_body_product|host_body_temp|host_color|host_common_name|host_diet|host_disease|host_dry_mass	|host_family_relationship|host_genotype|host_growth_cond|host_height|host_last_meal	|host_length|host_life_stage|host_phenotype	|host_sex|host_shape|host_subject_id|host_subspecf_genlin|host_substrate|host_symbiont|host_taxid|host_tissue_sampled|host_tot_mass|isolation_source|misc_param	|neg_cont_type|omics_observ_id|organism_count|oxy_stat_samp|perturbation|pos_cont_type|ref_biomaterial|rel_to_oxygen|samp_capt_status|samp_collect_device|samp_dis_stage|samp_mat_process|samp_salinity|samp_size|samp_store_dur|samp_store_loc|samp_store_temp|samp_vol_we_dna_ext|size_frac|source_material_id|temp|	description|
|-----------|---------------|-----------------------|------------|------------------|-------------------|----------------|-----------|--------------|-------|--------|--------|----------|---------|-------------------|-----------------|-----|----|-----------|---------|---------|----------------------|---------------------|-----------------|-----------------|--------------|----------|----------------|---------|------------|----------------|------------------------|---------------|--------------|-----------|---------------|-----------|---------------|---------------|--------|----------|---------------|--------------------|--------------|-------------|----------|-------------------|-------------|----------------|-----------|-------------|---------------|--------------||------------|------------|-------------|---------------|-------------||---------------|-------------------|--------------|----------------|-------------|---------|--------------|--------------|---------------|-------------------|---------|------------------|-----|------------|  
|MDEC_F4_B5_E1_A7|	FF1	|	metagenome	|2022-08-05|	coral reef [ENVO:00000150]|	marine coral reef flat zone| ENVO_01000144|	seawater	|Bermuda|	Not applicable	|32.371857°N 64.742464°W	|	| | | | | | | | |adult	| | | | | | | | | | | | | | | | |Adult| | | | | | | | |	Whole host organism	|	|seawater|	|	|	|	|	|	|	| | | | | |	DNA/RNA Shield|	|	A pool of 160uL which includes 40uL per sample (4 samples) |	|	|	|	|	|	|	samples were pooled from temperatures 16?, 22?,29? and 36?	


BioSamples submission ID SUB14410577
BioSample accession: SAMN47004859

At any time you can leave your submission and come back to it in the submission portal, it saves for you
Once the upload is ok, you review everything to make sure it's what you want, then submit your samples!
In the submission portal it will change the BioSample submission from in progress to processing. 
It doesn't take very long to process the samples. 
Once they are processed, each BioSample now has an associated SAMN# with it.

## 2 Create BioProject
Next the biosamples needs to be uploaded to NCBI. To do this you must first create a BioProject
Now you can go back to the BioSample submission portal and click BioProject in the blue box that says Start a New Submission.

- Again the first section of the portal is submitter info. There should be nothing to change but I advise to unclick Update my contact information in profile
![7](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/bioproject_1submitter.png?raw=true)

- The next section is project type. 
Choose all types of data that are applicable for this project. Usually raw sequence reads is one of them. If you have metagenome or holobiont sequences then targeted loci environmental is the type of sequence for that (target loci as the amplicon).
Choose the sample scope of the whole project, so be mindful if it is actually multi-species even though you are uploading data from one species at the moment.
![8](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/bioproject_2projecttype.png?raw=true)

- This section is for target information 
If this is a multispecies project, the target information allows you to describe each species.

- The next section is for the project information. Release information should be the same as for the BioSamples. Create a descriptive and informative title, and give more information in the public description. It is important to see that it says public, so this description needs to be clear and informative enough for someone from the public to be able to understand the data from this project and know if they want to use it for their purposes. For relevance, choose one of these in the dropdown menu.
Evolution
Medical
Agricultural
Industrial
Model Organism
Other
If you know the Grant ID associated with this project, now is a good time to add it, although it can be added later. If this is part of a Consortium initiative include that information as well.
![10](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/bioproject_4General_Info.png?raw=true)

- The next section is to add the BioSample SAMN#s that you created above. This is why it's easier to make these first instead of stopping in the middle of this submission to make them. If you make more later they can still be added in afterwards.
Then paste the SAMN# into the portal for the BioProject. Wait a second or two until the number comes up in the drop down and click on it to choose that sample.
- ![11](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/bioproject_5BioSample.png?raw=true)

## 3 SRA Sequence Upload
- Go to the Start a new submission tab in My submission and choose Sequence Read Archive and start a new submission.
![12](https://raw.githubusercontent.com/Putnam-Lab/Lab_Management/master/Bioinformatics_%26_Coding/images/new_sub.png)

- We will be doing the ftp upload, so make sure you can access your sequence via the command line
Again, the submitter info is the first step and shouldn't be changed.
For general information, paste in your new PRJNA# and say yes you have BioSamples (these will be added in the metadata sheet). Then choose the release data option that fits will all your other submissions.
![13](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/SRA_2GeneralInfo.png?raw=true)

- Next, for the metadata upload choose the Excel format and download the excel spreadsheet from NCBI.
![14](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/SRA_3metadata.png?raw=true)

- In the excel sheet, the first tab is general instructions about the file, the second tab is where you input your information. The third tab is all of the terms that can be used for Strategy, Source, Selection, and Platforms, as well as definitions of what they are (you should know these before hand, see information to gather before processing).
- In the first column add the SAMN# so NCBI can connect this data with the other information you have already put together.
- The SAMN# does not have information about your samples though, so in the library_ID include your sample number and identifying information (as in the BioSample_name) as well as the sequencing type as there might be multiple of those per sample. These will be the titles of the links on NCBI to your sequences.
- In the next column include a more descriptive title for each sample.
- Then in the next columns you need to add the sequencing information. These should generally be the same for all your samples if they were sequenced together. The answers to these are from the drop-down menus in each cell only. Look into the third tab of the spreadsheet for all options and definitions to choose the best one.
	- Library strategy
	- Library source
	- Library selection
	- Library layout
	- Sequencing platform
	- Sequence instrument model
	- Sequence file-type
- The design description column is where you should put all information about how the libraries were prepped, what method, and by whom.
- Then, in the filename column add the Read 1 complete file name. If you have paired end sequence data, add Read 2 in the filename 2 column of that same row. *note: make sure that your files are zipped with gzip and note zip.

- Please triple check that your file names match your sample number and matches your SAMN#. This is the most crucial. It is best to always stay in numerical order to help alleviate confusion, especially when you have to copy and paste single cells, or write out names completely.
- Then choose the tab-delimited version of the file for the metatdata upload in the SRA submission portal. It will check that everything is in the right format and flag you if something needs to be changed.
- Next you go to upload your files. Choose the FTP command line preload option and click the button to request a pre-load folder.
![16](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/master/images/SRA_images/SRA_4files.png?raw=true)

Set folder in Andromeda that only contains the symlinks to the raw sequences you want to upload to NCBI. To do this I will create a folder then add asymlink file to the sequence I want to upload.

```
mkdir ENCORE_raw_data/raw_files_rnaseq_sra
cd raw_files_rnaseq_sra
ln -s /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw/MDEC_R1_001.fastq .
ln -s /data/putnamlab/flofields/ENCORE_MDEC_denovo_transcriptome/data/raw/MDEC_R2_001.fastq .
```

Metadata for samples above found [here](https://github.com/flofields/ENCORE_Transcriptomes/blob/main/NCBI_Uploads/SRA_metadata.xlsx)
The path for downloading is /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra .

To upload files, log on to Andromeda and enter the following:

```
cd /data/putnamlab/flofields/ENCORE_raw_data/raw_files_rnaseq_sra/

ftp -i

open ftp-private.ncbi.nlm.nih.gov

# enter username and password given on SRA webpage 

cd uploads/ffields_uri.edu_QKj2JZGq

mkdir ENCORE_transcriptomes__upload_rnaseq

cd ENCORE_transcriptomes__upload_rnaseq

mput *
```
- Once that's done, you can go back to the submission portal and Select Preload folder. If your folder hasn't shown up yet, wait another 10 minutes and keep refreshing the folders. It can take time. Then select your folder and click use selected folder.

- Then, you review all your information, make sure all the files are there and submit!!

Note: It can take over a day for your sequences to show up on NCBI


