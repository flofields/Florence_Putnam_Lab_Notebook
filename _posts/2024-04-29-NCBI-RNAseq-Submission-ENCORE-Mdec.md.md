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
![4](https://github.com/flofields/Florence_Putnam_Lab_Notebook/blob/4773cd336c38a557052fe3a5eff504112800ba1b/images/SRA_images/general_information.jpg)
- click **Release immediately following processing and batch/Multiple Biosamples**.
- The thrid step is to fill out the sample type portion. Choose the sample type that your samples fit under. **Most often it is invertebrate. However if you know these samples include/are for holobiont or microbiome or symbiont sequences you want to choose the MIMS Environmental/Metagenome host associated excel sheet**  
![5](https://raw.githubusercontent.com/Putnam-Lab/Lab_Management/master/Bioinformatics_%26_Coding/images/MIMS.png)

- Step four, fill out the attributes page. choose upload a file using Excel or text format then download Excel option for the template

All the examples in this workflow are going to be with the Invertebrate sample type. If you're using the metagenome type most of these still apply, and other headings should be intuitive after looking through this.
Components of the downloaded spreadsheet to fill out (highlighted in green means mandatory):

Sample Name : unique name to identify your sample, can be abbreviated using consistent abbreviations from your data
Sample Title : not required, but you should write out a more descriptive name for your sample from your sample_name because these will be the link title when these samples are on NCBI
BioProject accession : you likely don't have this yet, so leave it blank if there isn't a project yet and it can be added in later
Organism : full scientific name of species. If you are using the metagenome file, say metagenome here.
Isolate, Breed, Host, or Isolation source : at least one of these is mandatory, if you don't have information on these you can say not applicable, missing, or not collected, whichever is appropriate.
Collected date : day the tissue/organism was taken
env_broad_scale : Add terms that identify the major environment type(s) where your sample was collected
env_local_scale : Add terms that identify environmental entities having causal influences upon the entity at time of sampling
env_medium : Add terms that identify the material displaced by the entity at time of sampling. Recommend subclasses of environmental material.
Geo-loc name : Word description of location; Country : State/Province : City/locality
lat_lon : The geographical coordinates of the location where the sample was collected. Specify as degrees latitude and longitude

