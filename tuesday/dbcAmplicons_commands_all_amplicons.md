Running the dbcAmplicons pipeline
===============================================

**ALL of this should only be done in an interactive session on the cluster**

srun -t 1-0 -c 2 -n 1 --mem 10000 --reservation workshop --pty /bin/bash

**1\.** First lets validate our install and environment

Change directory into the workshops space

	cd
	cd mca_example
	ls --color

you should see 4 directories: bin, Illumina_Reads, metadata, and src

Lets verify the software is accessible

	dbcVersionReport.sh

you should see the version info for dbcAmplicons, flash2 and RDP.

If all this is correct, we are ready to begin.

**1\.** First lets validate our input files:

# dbcAmplicons validate
	dbcAmplicons validate -h
	dbcAmplicons validate -B metadata/dbcBarcodeLookupTable.txt -P metadata/PrimerTable.txt -S metadata/workshopSamplesheet.txt

Once we are sure our input files are ok

**2\.** Lets Preprocess the data

# dbcAmplicons preprocess
	dbcAmplicons preprocess -h

First lets test preprocessing
	dbcAmplicons preprocess -B metadata/dbcBarcodeLookupTable.txt -P metadata/PrimerTable.txt -S metadata/workshopSamplesheet.txt -1 Illumina_Reads/Slashpile_only_R1.fastq.gz --test > preprocess.log

View preprocess.log and the file Identified_barcodes.txt, make sure the results make sense

	cat preprocess.log
	cat Identified_Barcodes.txt

Run all reads

	dbcAmplicons preprocess -B metadata/dbcBarcodeLookupTable.txt -P metadata/PrimerTable.txt -S metadata/workshopSamplesheet.txt -O Slashpile.intermediate -1 Illumina_Reads/Slashpile_only_R1.fastq.gz > preprocess.log

	cat preprocess.log
	cat Identified_Barcodes.txt

**3\.** Lets merge/join the read pairs

# dbcAmplicons Join
	dbcAmplicons join -h

	dbcAmplicons join -t 2 -O Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3 -1 Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3_R1.fastq.gz > join-16sV1V3.log
	cat join-16sV1V3.log

	dbcAmplicons join -t 2 -O Slashpile.intermediate/16sV4V5/Slashpile-16sV4V5 -1 Slashpile.intermediate/16sV4V5/Slashpile-16sV4V5_R1.fastq.gz > join-16sV4V5.log
	cat join-16sV4V5.log

	dbcAmplicons join -t 2 -O Slashpile.intermediate/ITS1/Slashpile-ITS1 -1 Slashpile.intermediate/ITS1/Slashpile-ITS1_R1.fastq.gz  > join-ITS1.log
	cat join-ITS1.log

	dbcAmplicons join -t 2 -O Slashpile.intermediate/ITS2/Slashpile-ITS2 -1 Slashpile.intermediate/ITS2/Slashpile-ITS2_R1.fastq.gz  > join-ITS2.log
	cat join-ITS2.log

	dbcAmplicons join -t 2 -O Slashpile.intermediate/LSU/Slashpile-LSU -1 Slashpile.intermediate/LSU/Slashpile-LSU_R1.fastq.gz > join-LSU.log
	cat join-LSU.log

**4\.** Classify the merged reads using RDP

# dbcAmplicons classify
	dbcAmplicons classify -h

	dbcAmplicons classify -p 2 --gene 16srrna -U Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3.extendedFrags.fastq.gz -O Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3

	dbcAmplicons classify -p 2 --gene 16srrna -U Slashpile.intermediate/16sV4V5/Slashpile-16sV4V5.extendedFrags.fastq.gz -O Slashpile.intermediate/16sV4V5/Slashpile-16sV4V5

	dbcAmplicons classify -p 2 --gene fungalits_unite -U Slashpile.intermediate/ITS1/Slashpile-ITS1.extendedFrags.fastq.gz -O Slashpile.intermediate/ITS1/Slashpile-ITS1

	dbcAmplicons classify -p 2 --gene fungalits_unite -U Slashpile.intermediate/ITS2/Slashpile-ITS2.extendedFrags.fastq.gz -O Slashpile.intermediate/ITS2/Slashpile-ITS2

	dbcAmplicons classify -p 2 --gene fungallsu -1 Slashpile.intermediate/LSU/Slashpile-LSU.notCombined_1.fastq.gz -2 Slashpile.intermediate/LSU/Slashpile-LSU.notCombined_2.fastq.gz -O Slashpile.intermediate/LSU/Slashpile-LSU

---

**5\.** Produce Abundance tables

	mkdir Slashpile.results

	dbcAmplicons abundance -h

	dbcAmplicons abundance -S metadata/workshopSamplesheet.txt -O Slashpile.results/16sV1V3 -F Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3.fixrank --biom > abundance.16sV1V3.log
	cat abundance.16sV1V3.log

	dbcAmplicons abundance -S metadata/workshopSamplesheet.txt -O Slashpile.results/16sV4V5 -F Slashpile.intermediate/16sV4V5/Slashpile-16sV4V5.fixrank --biom  > abundance.16sV4V5.log
	cat abundance.16sV4V5.log

	dbcAmplicons abundance -S metadata/workshopSamplesheet.txt -O Slashpile.results/ITS1 -F Slashpile.intermediate/ITS1/Slashpile-ITS1.fixrank --biom  > abundance.ITS1.log
	cat abundance.ITS1.log

	dbcAmplicons abundance -S metadata/workshopSamplesheet.txt -O Slashpile.results/ITS2 -F Slashpile.intermediate/ITS2/Slashpile-ITS2.fixrank  --biom > abundance.ITS2.log
	cat abundance.ITS2.log

	dbcAmplicons abundance -S metadata/workshopSamplesheet.txt -O Slashpile.results/LSU -F Slashpile.intermediate/LSU/Slashpile-LSU.fixrank --biom > abundance.LSU.log
	cat abundance.LSU.log

**6\.** Split Reads by samples, for downstream processing in another application (post preprocessing/merging), or for submission to the SRA.

	splitReadsBySample.py -h
	splitReadsBySample.py -O SplitBySample/16sV1V3 -1 Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3_R1.fastq.gz -2 Slashpile.intermediate/16sV1V3/Slashpile-16sV1V3_R2.fastq.gz

**FYI** Preprocessing Pairs with inline BC, Mills lab protocol

	preproc_Pair_with_inlineBC.py -h
