 ---
LAYOUT: post
TITLE: Workflow for Diploria labyrinthiformis functional annotation
CATEGORY: [ de novo transcriptome, RNA seq ]
TAG: [ Diploria labyrinthiformis, Bioinfamatics, de novo transcriptome ]
---
## Designing a workflow for a functional annotation for *Diploria labyrinthiformis* de novo transcriptome.

**About**: This post documents the pipline used to identify orthologous genes across species for a comparative analyses and the functional annotation of the created de novo transcriptome for *Diploria labyrinthiformis* using samples obtained in August 2022 as part of the ENCORE project in the TPC-PI experiment. 

Use Uniprot and Pfam databases after TransDecoder to convert the nucleotides into amino acid sequences then ensure the gene IDs are species specfic for easy identification when clustering orthologs from 3 species.

### Step 1. Download UniProt and Pfam Databases

	mkdir /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot
	cd /databases/uniprot

	# Download Swiss-Prot (curated subset)
	wget -O /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot/uniprot_sprot.fasta.gz \
		https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz

	  gunzip /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot/uniprot_sprot.fasta.gz

	  srun --nodes=1 --ntasks=4 --mem=2G --time=00:30:00 --pty bash
	  module load uri/main BLAST+/2.15.0-gompi-2023a
	
	  makeblastdb -in uniprot_sprot.fasta -dbtype prot

	  cd ..
	  mkdir pfam
	  cd pfam/

	 wget -O /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm.gz \
	  http://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.gz

	  # Unzip and index
	  gunzip /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm.gz
	  hmmpress /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm

### Step 2: Obtain ORFs

Identifies all open reading frames i.e ORFs (coding regions) that meet a minimum length threshold (default is 100 amino acids) and translates them into peptide sequences

#### Dlab

	module load uri/main TransDecoder/5.5.0-GCC-11.3.0

	#file in dir /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/cd-hit
	# -redundancy-minimized set includes 3586 / 5000 = 71.72%
	TransDecoder.LongOrfs -t dlab_cdhit.fasta

#### Mdec

	#file in dir /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/cd-hit
	# -redundancy-minimized set includes 4054 / 5000 = 81.08%
	TransDecoder.LongOrfs -t mdec_cdhit.fasta

#### Mcav
	#file in dir /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/cd-hit
	TransDecoder.LongOrfs -t mcav_cdhit.fasta
	
Note: after this step a meaasge appears:

	Use file: /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/data/cd-hit/mdec_cdhit.fasta.transdecoder_dir/longest_orfs.pep  for Pfam and/or BlastP searches to enable homology-based coding region identification.

        Then, run TransDecoder.Predict for your final coding region predictions.
	

### Step 3: Run BLASTP and Pfam Searches (via SBATCH script)
#### Dlab
	nano /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/scripts/Blastp_Pfam_search

	#!/bin/bash
	#SBATCH --job-name=blastp_dlab
	#SBATCH --nodes=1 --cpus-per-task=15
	#SBATCH --mem=200G  # Requested Memory
	#SBATCH --time=24:00:00
	#SBATCH -o slurm-dlab.out  # %j = job ID
	#SBATCH -e slurm-dlab.err  # %j = job ID
	#SBATCH --mail-type=BEGIN,END,FAIL
	#SBATCH --mail-user=ffields@uri.edu
	#SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/searches
	#SBATCH --constraint=avx512

	module load uri/main BLAST+/2.15.0-gompi-2023a
	module load uri/main HMMER/3.4-gompi-2023a

	echo "Blastp search" $(date)
	blastp -query /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/cd-hit/dlab_cdhit.fasta.transdecoder_dir/longest_orfs.pep \
		-db /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot/uniprot_sprot.fasta \
		-evalue 1e-5 \
		-max_target_seqs 5 \
		-outfmt 6 \
		-num_threads 8 > blastp.outfmt6

	echo "pfam search" $(date)

	hmmscan --cpu 8 \
		--domtblout pfam.domtblout \
		/scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm \
		/scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/cd-hit/dlab_cdhit.fasta.transdecoder_dir/longest_orfs.pep

	echo "Done. All outputs saved." $(date)

sbatch /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/scripts/Blastp_Pfam_search
Submitted batch job 44042346

#### Mdec
nano /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/Blastp_Pfam_search

	#!/bin/bash
	#SBATCH --job-name=blastp_mdec
	#SBATCH --nodes=1 --cpus-per-task=15
	#SBATCH --mem=200G  # Requested Memory
	#SBATCH --time=24:00:00
	#SBATCH -o slurm-mdec.out  # %j = job ID
	#SBATCH -e slurm-mdec.err  # %j = job ID
	#SBATCH --mail-type=BEGIN,END,FAIL
	#SBATCH --mail-user=ffields@uri.edu
	#SBATCH -D /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/data/searches
	#SBATCH --constraint=avx512

	module load uri/main BLAST+/2.15.0-gompi-2023a
	module load uri/main HMMER/3.4-gompi-2023a

	echo "Blastp search" $(date)
	blastp -query /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/data/cd-hit/mdec_cdhit.fasta.transdecoder_dir/longest_orfs.pep \
		-db /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot/uniprot_sprot.fasta \
		-evalue 1e-5 \
		-max_target_seqs 5 \
		-outfmt 6 \
		-num_threads 8 > blastp.outfmt6

	echo "pfam search" $(date)

	hmmscan --cpu 8 \
		--domtblout pfam.domtblout \
		/scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm \
		/scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/data/cd-hit/mdec_cdhit.fasta.transdecoder_dir/longest_orfs.pep

	echo "Done. All outputs saved." $(date)

```
sbatch  /scratch3/workspace/ffields_uri_edu-transcriptomes/mdec/scripts/Blastp_Pfam_search
Submitted batch job 44041381
```

#### Mcav
nano /scratch3/workspace/ffields_uri_edu-transcriptomes/mcav/scripts/Blastp_Pfam_search

	#!/bin/bash
	#SBATCH --job-name=blastp_mcav
	#SBATCH --nodes=1 --cpus-per-task=15
	#SBATCH --mem=200G  # Requested Memory
	#SBATCH --time=24:00:00
	#SBATCH -o slurm-mcav.out  # %j = job ID
	#SBATCH -e slurm-mcav.err  # %j = job ID
	#SBATCH --mail-type=BEGIN,END,FAIL
	#SBATCH --mail-user=ffields@uri.edu
	#SBATCH -D /scratch3/workspace/ffields_uri_edu-transcriptomes/mcav/data/searches
	#SBATCH --constraint=avx512

	module load uri/main BLAST+/2.15.0-gompi-2023a
	module load uri/main HMMER/3.4-gompi-2023a

	echo "Blastp search" $(date)
	blastp -query /scratch3/workspace/ffields_uri_edu-transcriptomes/mcav/data/cd-hit/mcav_cdhit.fasta.transdecoder_dir/longest_orfs.pep \
	-db /scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/uniprot/uniprot_sprot.fasta \
	-evalue 1e-5 \
	-max_target_seqs 5 \
	-outfmt 6 \
	-num_threads 8 > blastp.outfmt6

	echo "pfam search" $(date)

	hmmscan --cpu 8 \
	--domtblout pfam.domtblout \
	/scratch3/workspace/ffields_uri_edu-transcriptomes/dlab/data/databases/pfam/Pfam-A.hmm \
	/scratch3/workspace/ffields_uri_edu-transcriptomes/mcav/data/cd-hit/mcav_cdhit.fasta.transdecoder_dir/longest_orfs.pep

	echo "Done. All outputs saved." $(date)

```
sbatch  /scratch3/workspace/ffields_uri_edu-transcriptomes/mcav/scripts/Blastp_Pfam_search
Submitted batch job 44043202
```

### Step 4: Renaming the gene ID headers for identification purposes

	sed 's/^>/\>dlab/' dlab_cdhit.fasta > dlab_cdhit_m.fasta
	sed 's/^>/\>mcav/' mcav_cdhit.fasta > mcav_cdhit_m.fasta
	sed 's/^>/\>mdec/' mdec_cdhit.fasta > mdec_cdhit_m.fasta

### Step 5: Run TransDecoder with Homology Support
It takes the ORFs identified from TransDecoder.LongOrfs and predicts which ones are coding sequences using the hits from blastp and pfam.

#### Dlab

	srun --nodes=1 --ntasks=4 --mem=5G --time=00:30:00 --pty bash
	module load uri/main TransDecoder/5.5.0-GCC-11.3.0

	TransDecoder.Predict -t dlab_cdhit_m.fasta \
	  --retain_blastp_hits /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/searches/blastp.outfmt6 \
	  --retain_pfam_hits /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/searches/pfam.domtblout
 
#### Mdec

	TransDecoder.Predict -t mdec_cdhit.fasta \
	  --retain_blastp_hits /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/searches/blastp.outfmt6 \
	  --retain_pfam_hits /scratch/workspace/ffields_uri_edu-transcriptomes/mdec/data/searches/pfam.domtblout
 
 #### Mcav

	TransDecoder.Predict -t mcav_cdhit.fasta \
	  --retain_blastp_hits /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/searches/blastp.outfmt6 \
	  --retain_pfam_hits /scratch/workspace/ffields_uri_edu-transcriptomes/mcav/data/searches/pfam.domtblout
 
 ### Step 5: Renaming the gene ID headers for identification purposes

	sed 's/^>/dlab|/' dlab_cdhit.fasta.transdecoder.pep > dlab.faa
	sed 's/^>/\>dlab_/' dlab_cdhit.fasta.transdecoder.pep > dlab.faa
	sed 's/^>/\>mcav_/' mcav_cdhit.fasta.transdecoder.pep > mcav.faa
	sed 's/^>/\>mdec_/' mdec_cdhit.fasta.transdecoder.pep > mdec.faa
	
TransDecoder.Predict -t mcav_cdhit.fasta
sed 's/^>/\>mcav_/' longest_orfs.pep > mcav.faa

sed 's/^>/\>mdec_/' species1.Trinity.fasta > mdec.Trinity.ID.fasta
sed 's/^>/\>species1_/' species1.faa > species1.ID.faa

TransDecoder.LongOrfs -t species1_transcripts.fasta
TransDecoder.Predict -t dlab_cdhit.fasta

mv Trinity.fasta.transdecoder.pep species1.faa

#!/usr/bin/env bash
#SBATCH --export=NONE
#SBATCH --ntasks=1 --cpus-per-task=20 #split one task over multiple CPU
#SBATCH --mem=500GB
#SBATCH -p cpu-preempt
#SBATCH -t 48:00:00
#SBATCH --mail-type=END,FAIL,TIME_LIMIT_80 #email you when job stops and/or fails or is nearing its time limit
#SBATCH --error=scripts/outs_errs/"%x_error.%j" #if your job fails, the error report will be put in this file
#SBATCH --output=scripts/outs_errs/"%x_output.%j" #once your job is completed, any final job report comments will be put in this file
#SBATCH -D 
#load conda and activate conda environment
module load conda/latest
conda activate /work/pi_hputnam_uri_edu/conda/envs/env-broccoli

#load additional programs needed to run broccoli
module load uri/main
module load diamond/2.1.10
module load all/FastTree/2.1.11-GCCcore-12.3.0

mkdir -p output/broccoli

cd output/broccoli
python ~/broccoli.py -dir ../../cnidarian_protein_fastas/ -ext '.faa' -path_fasttree FastTree -threads 8


# Functional anotation

#!/bin/bash
#SBATCH --job-name=dlab-fa
#SBATCH --nodes=1 --cpus-per-task=15
#SBATCH --mem=500G  # Requested Memory
#SBATCH -t 48:00:00
#SBATCH -o slurm-dlab-fa.out  # %j = job ID
#SBATCH -e slurm-dlab-fa.err  # %j = job ID
#SBATCH --mail-type=END,FAIL #email you when job starts, stops and/or fails
#SBATCH --mail-user=federica.scucchia@uri.edu #your email to send notifications
#SBATCH -D /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/functional_annotation


# load modules needed
#module load diamond/2.1.10
module load blast-plus/2.14.1
#module load uri/main all/InterProScan/5.60-92.0-foss-2021b

diamond blastp \
	-d uniprot_sprot.dmnd \
	-q /scratch/workspace/ffields_uri_edu-transcriptomes/dlab/data/cd-hit/dlab.faa \
	-o \
	-f 6 -b 4 --more-sensitive -e 0.00001 
	-k 1 -e 1e-5 --threads 8
	


# Run InterProScan
#interproscan.sh -f TSV -i 

note:look for packages that remove ribosomal rnas from transcriptomes 

/scratch3/workspace/ffields_uri_edu-transcriptomes